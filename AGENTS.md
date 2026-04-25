# Agent Instructions: docker-image

## Project Purpose
Multi-image Docker build repository. Images are built via GitHub Actions and pushed to Tencent Cloud Container Registry (TCR).

## Repository Structure
```
docker/
  <image-name>/          # One directory per image
    Dockerfile
.github/workflows/
  build-<image-name>.yml # One workflow per image (convention)
```

## Conventions

### Image Directory Naming
- Directory under `docker/` **must match** the workflow `paths` filter and `context` value.
- Example: `docker/ifr-ros2-base/` → workflow `paths: 'docker/ifr-ros2-base/**'` and `context: docker/ifr-ros2-base`.

### Workflow Pattern (copy-paste template)
All images follow the same workflow structure. When adding a new image:
1. Copy `.github/workflows/build-ifr-ros2-base.yml`.
2. Replace `ifr-ros2-base` with the new image name everywhere (filename, `paths`, `context`, tags).
3. Update `runs-on` only if the build requires a larger runner.

### Tag Format
```
{commit-short-sha}-{YYYYMMDDHHMMSS}
```
Example: `a1b2c3d4-20260426123045`

Always tag `latest` alongside the version tag.

### Registry & Namespace
- Registry: `ccr.ccs.tencentyun.com`
- Namespace: `ws-ifr`
- Full image path: `ccr.ccs.tencentyun.com/ws-ifr/<image-name>:<tag>`

### Required Repository Secrets
| Secret | Value |
|--------|-------|
| `TENCENT_CLOUD_REGISTRY` | `ccr.ccs.tencentyun.com` |
| `TENCENT_CLOUD_USERNAME` | `100009960526` |
| `TENCENT_CLOUD_PASSWORD` | *(supplied by user)* |

## Existing Images

### `ifr-ros2-base`
- **Base image**: `ros:foxy-ros-base`
- **Key deps**: OpenCV 4.13.0 (+ contrib), Eigen3
- **OpenCV source**: Downloaded from GitHub (`wget`) during build, **not** copied from local.
- **Mirrors**: apt → Tsinghua; pip → Tsinghua.
- **User**: `ifr` (UID/GID 1000)
- **Trigger**: push to `main` affecting `docker/ifr-ros2-base/**`
