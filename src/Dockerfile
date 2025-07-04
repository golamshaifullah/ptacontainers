# Use an NVIDIA CUDA base image with runtime libraries
FROM nvidia/cuda:11.8.0-cudnn8-runtime-ubuntu22.04

# Make /tmp a working dir for conda install steps
WORKDIR /tmp

# Copy discovery-gpu.yml into the container
COPY discovery-gpu.yml /tmp/discovery-gpu.yml

# Use bash as our shell (so conda commands work as expected in RUN)
SHELL ["/bin/bash", "-c"]

# System packages + install Miniconda + create environment
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    bzip2 \
    ca-certificates \
    git \
    build-essential \
    && rm -rf /var/lib/apt/lists/* \
    \
    # Install Miniconda
    && wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh \
    && bash miniconda.sh -b -p /opt/conda \
    && rm -f miniconda.sh \
    \
    # Add conda to PATH
    && export PATH="/opt/conda/bin:$PATH" \
    \
    # Configure conda channels
    && conda config --system --add channels conda-forge \
    && conda config --system --add channels nvidia \
    && conda config --system --add channels defaults \
    && conda config --system --set channel_priority flexible \
    \
    # Create your GPU-enabled environment from the environment.yml
    && conda env create -f /tmp/discovery-gpu.yml \
    \
    # Clean up conda pkgs
    && conda clean --all --yes

# By default, Docker won't "auto-activate" conda environments.
# We'll just put the environment in PATH:
ENV PATH="/opt/conda/envs/gpu-env/bin:${PATH}"

# Use /workspace as a convenient working directory
WORKDIR /workspace

# Default command: run Python in the GPU-enabled environment
CMD ["python"]
