FROM ubuntu:22.04

ARG TARGETARCH
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get -y update && apt-get install -y --no-install-recommends \
    build-essential wget git ca-certificates coreutils \
    && rm -rf /var/lib/apt/lists/* \
    && git --version

ENV CONDA_DIR=/opt/conda
ENV PATH=${CONDA_DIR}/bin:${PATH}

RUN case "${TARGETARCH}" in \
      "arm64") MINICONDA_ARCH="aarch64" ;; \
      "amd64") MINICONDA_ARCH="x86_64" ;; \
      *) echo "Unsupported architecture: ${TARGETARCH}" && exit 1 ;; \
    esac \
    && wget "https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-${MINICONDA_ARCH}.sh" -O /tmp/miniconda.sh \
    && bash /tmp/miniconda.sh -b -p ${CONDA_DIR} \
    && rm /tmp/miniconda.sh \
    && ${CONDA_DIR}/bin/conda clean -afy

COPY container/ml-dependencies.yml /tmp/ml-dependencies.yml
RUN conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main \
    && conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r \
    && conda env create -f /tmp/ml-dependencies.yml \
    && conda clean -afy

ENV PATH=${CONDA_DIR}/envs/ml-dependencies/bin:${PATH}
WORKDIR /workspaces/ml.school
