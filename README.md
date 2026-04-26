# docker-image

GitHub Actions 自动构建 Docker 镜像并推送到腾讯云容器镜像服务（TCR）。

## 镜像列表

| 镜像            | 说明                                                                      | 路径                    | 最新                                                 |
| --------------- | ------------------------------------------------------------------------- | ----------------------- | ---------------------------------------------------- |
| `ifr-ros2-base` | ROS2 Foxy <br/> OpenCV 4.13 <br/> Eigen3 <br/> GCC 13 <br/> Clangd 22.1.4 | `docker/ifr-ros2-base/` | `ccr.ccs.tencentyun.com/ws-ifr/ifr-ros2-base:latest` |

## 仓库地址

```
ccr.ccs.tencentyun.com/ws-ifr/<镜像名>:<标签>
```

标签格式：`{commit 前 8 位}-{YYYYMMDDHHMMSS}`，同时会推送 `latest`。

## 新增镜像

1. 在 `docker/` 下创建 `<镜像名>/Dockerfile`
2. 复制 `.github/workflows/build-ifr-ros2-base.yml`，替换为新的镜像名
3. push 到 `main` 分支即自动构建
