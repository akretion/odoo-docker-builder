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

RUN pip install git-aggregator
COPY install/* /install/

# pre-fetch the git-autoshare cache with the bare minimum:
# (git-autoshare reference cannot be shallow at all sadly)
ADD https://api.github.com/repos/odoo/odoo/git/refs/heads/14.0 version.json
RUN mkdir -p /odoo/src && \
    cd /odoo/src && \
    git init && \
    git remote add odoo https://github.com/odoo/odoo.git && \
    git fetch odoo 14.0 --shallow-since=2020-10-01 && \
    git remote add oca https://github.com/oca/ocb.git && \
    git fetch oca 14.0 --shallow-since=2020-10-01
