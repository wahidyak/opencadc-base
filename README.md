# OpenCADC Base Images

This repository contains the layered Dockerfile definitions for the OpenCADC/CANFAR container ecosystem.

## Architecture

The images follow a strictly layered, multi-stage Docker model to ensure consistency, reproducibility, and auditability:

1. **Runtime Layer (Stage 1: `runtime`)**: 
   - OS: Ubuntu 24.04 LTS (Digest Pinned)
   - Runtime: Micromamba + Python 3.13
   - Tools: `uv`, `pixi` (Version Pinned)
   - Focus: OS foundation and language runtime stability.
2. **Science Layer (Stage 2: `science`)**: 
   - Inherits from **Runtime**
   - Science Stack: `cadcdata`, `cadctap`, `cadcutils`, `canfar`, `vos`
   - Focus: Domain-specific tools and library ecosystem.
3. **Terminal Image**: 
   - Inherits from **Science**
   - Adds: `ttyd`, `starship` (Pinned), `tmux`, `htop`, `rclone`, `jq`
4. **Terminal OC Image**: 
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
- **Persistence**: Optimized for CANFAR Skaha. Conda environments, Pip packages (`--user`), UV cache, and Pixi homes are redirected to the user's persistent `${HOME}` directory.
- **Environment**: Modern managers like `uv` and `pixi` are pre-configured with shell completions.
