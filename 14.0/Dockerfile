FROM python:3.10.12-alpine3.18

RUN apk fix && \
    apk --no-cache --update add git git-lfs gpg less openssh patch bash && \
    git lfs install

# we cannot use a more recent version for now
# see https://github.com/yaml/pyyaml/issues/724
# note that we install ak and git-aggregator to be able to do ak-build
# later in a builder image based on this image and ensure odoo/src
# is converged to the proper revision with the proper merges
RUN pip install PyYaml==5.3.1
RUN pip install git+https://github.com/akretion/ak

COPY repos.yml /root/.config/git-autoshare/repos.yml
RUN pip install git-autoshare git-aggregator
COPY bin/* /usr/local/bin/
COPY install/* /install/

# pre-fetch the git-autoshare cache with the bare minimum:
ADD https://api.github.com/repos/odoo/odoo/git/refs/heads/14.0 version.json
RUN mkdir -p /root/.cache/git-autoshare/github.com/odoo && \
    cd /root/.cache/git-autoshare/github.com/odoo && \
    /usr/bin/git init --bare && \
    /usr/bin/git fetch https://github.com/OCA/OCB.git refs/heads/14.0:refs/git-autoshare/oca/heads/14.0 --shallow-since=2021-10-01
