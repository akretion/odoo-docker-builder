# Odoo builder images

These auxiliary images are designed to be used in a multi-stages Docker build.
see https://docs.docker.com/build/building/multi-stage/

With traditional Odoo Dockerfiles, every time your base image get security updates (often once a week), all the Docker lines caches are swept and you rebuild the world, downloading 15 years of Odoo git history that weights 5.7 Gb. It can easily take 20 min in the cloud to 1h30 with a bad onsite connection...

With these base images, we pack the bare git-autoshare object references minimum to make the build always fast and light: git history objects are never copied in the production image, only the <250 Mb worktree is. The auxiliary base image weights also no more than 280 Mb because we use fetch --shallow-since the release date: so we can get the Odoo PR merges without carrying the whole "fatty odoo" git repo.

Cached builds still take less than 3 seconds while in the worst case, builds after security updates will not download anything from odoo/odoo, only copy the 250 Mb local worktree. And odoo-spec.yaml updates will not trigger large downloads either, only the git delta will be fetched. Hence even in the worst cases builds don't exceed 2 minutes.

Only a standard Dockerfile, no host hack.
