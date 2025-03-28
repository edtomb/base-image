# Building

This Dockerfile relies on the structure provided by [Vast.Ai Base images](https://github.com/vast-ai/base-image).
### Docker Options (VastAI Template)
```
-p 1111:1111 -p 8080:8080 -p 8384:8384 -p 72299:72299 -p 7860:7860 -e OPEN_BUTTON_PORT=1111 -e OPEN_BUTTON_TOKEN=1 -e JUPYTER_DIR=/ -e DATA_DIRECTORY=/workspace/ -e PORTAL_CONFIG="localhost:1111:11111:/:Instance Portal|localhost:7860:17860:/:SDNext|localhost:8080:18080:/:Jupyter|localhost:8080:18080:/terminals/1:Jupyter Terminal|localhost:8384:18384:/:Syncthing" -e PROVISIONING_SCRIPT=https://raw.githubusercontent.com/vast-ai/base-image/refs/heads/main/derivatives/pytorch/derivatives/sdnext/provisioning_scripts/default.sh -e SDNEXT_ARGS="--port 17860"
See the example below for building instructions.
```

```bash
docker buildx build \
    --platform linux/amd64 \
    --build-arg PYTORCH_BASE=vastai/pytorch:2.7.0-cuda-12.8.0-py311-ipv2 \
    --build-arg SDNEXT_REF=master \
    . -t repo/image:tag --push
```
