ARG PYTORCH_BASE=vastai/pytorch:2.7.0-cuda-12.8.0-py311-ipv2

FROM ${PYTORCH_BASE}

# Maintainer details
LABEL org.opencontainers.image.source="https://github.com/vastai/"
LABEL org.opencontainers.image.description="SD.Next image suitable for Vast.ai."
LABEL maintainer="Vast.ai Inc <contact@vast.ai>"

# Copy Supervisor configuration and startup scripts
COPY ./ROOT /

# Required or we will not build
ARG SDNEXT_REF

RUN \
    set -euo pipefail && \
    [[ -n "${SDNEXT_REF}" ]] || { echo "Must specify SDNEXT_REF" && exit 1; } && \
    . /venv/main/bin/activate && \
    # We have PyTorch pre-installed so we will check at the end of the install that it has not been clobbered
    torch_version_pre="$(python -c 'import torch; print (torch.__version__)')" && \
    # Skip install xformers because it doesn't work with pytorchcu12.8
    pip install torch==$PYTORCH_VERSION --index-url "${PYTORCH_INDEX_URL}" && \
    pip install onnxruntime-gpu insightface && \
    # Get SD Next and install dependencies (torch should not be pinned to a specific version in the requirements.txt - If it is then our build will probably fail)
    cd /opt/workspace-internal/ && \
    git clone https://github.com/vladmandic/sdnext && \
    cd /opt/workspace-internal/sdnext && \
    git checkout "${SDNEXT_REF}" && \
    pip install --no-cache-dir \
        -r requirements.txt && \
    LD_PRELOAD=libtcmalloc_minimal.so.4 \
        python launch.py \
            --quick \
            --port 11404 \
            --test && \
    # Test 1: Verify PyTorch version is unaltered
    torch_version_post="$(python -c 'import torch; print (torch.__version__)')" && \
    [[ $torch_version_pre = $torch_version_post ]] || { echo "PyTorch version mismatch (wanted ${torch_version_pre} but got ${torch_version_post})"; exit 1; }
