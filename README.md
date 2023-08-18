# Odoo builder images

These auxiliary images are optimized to be used in a multi-stages Docker Odoo build.
see https://docs.docker.com/build/building/multi-stage/

With traditional Odoo Dockerfiles, every time your base image get security updates (often once a week), all the Docker lines caches are swept and you rebuild the world, downloading 15 years of Odoo git history that weights 5.7 Gb. It can easily take 20 min in the cloud to 1h30 with a bad onsite connection...

In these builder base images, we prepare /odoo/src with the proper Odoo serie single branch checkout along the the OCA/OCB backports. You can use it at your Odoo or OCB base and merge any pull requests you need atop of it (don't tell me your Odoo pull requests were merged, I don't believe you).

We tried several approaches, like shallow clones. But we found out that in order to get extra pull requests merged efficiently, the best was a full single branch fetch. The images typically weight around 550 Mb with the Odoo serie branch and the OCA/OCB backport. By contrast, shallow clones with OCB weight (only) 100 Mb less but take way longer to merge any extra pull request so full single branch fetches are better. Also using git-autoshare is useless here and would in fact mean more disk writes to do the initial /odoo/src checkout.

Using these auxiliary images, cached builds still take less than 3 seconds while in the worst case, builds after security updates will not download anything from odoo/odoo, only copy the 250 Mb local worktree. And odoo-spec.yaml updates will not trigger large downloads either, only the git delta will be fetched. Hence even in the worst cases builds don't exceed 2 minutes.

Only a standard Dockerfile, no host hack.
