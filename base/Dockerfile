ARG UBUNTU_VERSION=22.04
ARG PYTHON_VERSION=3.13

FROM ubuntu:${UBUNTU_VERSION} AS base

LABEL maintainer="CANFAR Project <support@canfar.net>"
LABEL org.opencontainers.image.source="https://github.com/opencadc/opencadc-base"

USER root
WORKDIR /tmp

# Set environment variables
ENV TZ=PST
ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

# Layer 0: OS - System Foundation
RUN apt-get update --yes --quiet --fix-missing \
    && apt-get install --yes --quiet \
        acl \
        bzip2 \
        curl \
        git \
        locales \
        nano \
        rsync \
        openssh-client \
        sudo \
        tzdata \
        vim \
        wget \
        zsh \
        tcsh \
    && apt-get clean --yes \
    && rm -rf /var/lib/apt/lists/* /var/tmp/* \
    && ln -fs /usr/share/zoneinfo/${TZ} /etc/localtime \
    && dpkg-reconfigure --frontend noninteractive tzdata \
    && locale-gen en_US.UTF-8

# Layer 1: Runtime (Python via Micromamba)
ENV CONDA_DIR=/opt/conda
ENV PATH="${CONDA_DIR}/bin:${PATH}"
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1

RUN set -x \
    && curl -Ls https://micromamba.snakepit.net/api/micromamba/linux-64/latest | tar -xvj bin/micromamba \
    && ./bin/micromamba install \
        --root-prefix="${CONDA_DIR}" \
        --prefix="${CONDA_DIR}" \
        --yes \
        python=${PYTHON_VERSION} mamba \
    && rm -rf ./bin/micromamba

# Install CADC Science Stack & baseline tools
RUN mamba install --yes \
        conda \
        pip \
        conda-libmamba-solver \
        pip-tools \
    && pip install --no-cache-dir \
        cadcdata \
        cadctap \
        cadcutils \
        canfar \
        vos \
    && mamba clean --all --quiet --yes

WORKDIR /root
CMD ["/bin/bash"]
