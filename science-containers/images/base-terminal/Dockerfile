# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG REGISTRY=images.canfar.net
ARG OWNER=canfar
ARG BASE_CONTAINER=$REGISTRY/$OWNER/base-3.11
FROM ${BASE_CONTAINER}

LABEL maintainer="CANFAR Project <supprt@canfar.net>"

USER root

# Install all OS dependencies for Server that starts but lacks all
# features (e.g., download as all possible file formats)
RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    fonts-liberation \
    ca-certificates \
    # - tini is installed as a helpful container entrypoint that reaps zombie processes and such of the actual executable we want to start, see https://github.com/krallin/tini#why-tini for details.
    tini \
    # - run-one - a wrapper script that runs no more than one unique  instance  of  some  command with a unique set of arguments, we use `run-one-constantly` to support `RESTARTABLE` option
    run-one && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

COPY initial-condarc "${CONDA_DIR}/.condarc"

# Install JupyterLab
# Generate a Jupyter Server config
# Cleanup temporary files
WORKDIR /tmp
RUN mamba install --yes \
    'xterm' && \
    mamba clean --all -f -y

# Configure container startup
# The ENTRYPOINT specifies a command that will always be executed when the container starts.
# The CMD specifies arguments that will be fed to the ENTRYPOINT.
# Instruction to provide executables that will always execute when the container is launched.
ENTRYPOINT ["tini", "-g", "--"]

# Fix permissions on /etc/jupyter as root
USER root

# While the ARG variable is unavailable after the image-building process, ENV persists in the containers.
ENV HOME /root
WORKDIR ${HOME}