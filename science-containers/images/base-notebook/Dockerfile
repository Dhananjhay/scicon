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
    # - pandoc is used to convert notebooks to html files
    #   it's not present in aarch64 ubuntu image, so we install it here
    pandoc \
    # - run-one - a wrapper script that runs no more than one unique  instance  of  some  command with a unique set of arguments, we use `run-one-constantly` to support `RESTARTABLE` option
    run-one && \
    apt-get clean && rm -rf /var/lib/apt/lists/* && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

COPY initial-condarc "${CONDA_DIR}/.condarc"

# Install JupyterLab
# Generate a Jupyter Server config
# Cleanup temporary files
WORKDIR /tmp
RUN mamba install --yes \
    'jupyterlab' && \
    jupyter server --generate-config && \
    mamba clean --all -f -y && \
    jupyter lab clean

ENV JUPYTER_PORT=8888
EXPOSE $JUPYTER_PORT

# Fix permissions on /etc/jupyter as root
USER root

# While the ARG variable is unavailable after the image-building process, ENV persists in the containers.
ENV HOME /root
WORKDIR ${HOME}