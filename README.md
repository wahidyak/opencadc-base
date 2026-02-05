# OpenCADC Base Images

This repository contains the layered Dockerfile definitions for the OpenCADC/CANFAR container ecosystem.

## Architecture

The images follow a strictly layered model to ensure consistency and minimize build times:

1. **Base Image (`base/`)**: 
   - OS: Ubuntu 22.04 LTS
   - Runtime: Micromamba + Python 3.13
   - Science Stack: `cadcdata`, `cadctap`, `cadcutils`, `canfar`, `vos`
2. **Terminal Image (`terminal/`)**: 
   - Inherits from **Base**
   - Adds: `ttyd`, `starship` (Gruvbox theme), `tmux`, `htop`, `rclone`, `jq`
3. **Terminal OC Image (`terminal-oc/`)**: 
   - Inherits from **Terminal**
   - Adds: **OpenCode AI** integration

## Build Instructions

Because these images inherit from each other, they must be built in order:

```bash
# 1. Build the Base
docker build -t opencadc-base:latest ./base

# 2. Build the Standard Terminal
docker build -t opencadc-terminal:latest ./terminal

# 3. Build the Terminal with OpenCode
docker build -t opencadc-terminal-oc:latest ./terminal-oc
```

## Configuration

- **Ports**: All terminal images expose port `5000` for `ttyd`.
- **Theme**: Uses the Gruvbox palette via Starship.
- **Aliases**: `py` -> `python3`, `oc` -> `opencode`, and standard navigation shortcuts.
