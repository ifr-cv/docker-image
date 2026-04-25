# 基础镜像
FROM ros:foxy-ros-base

LABEL maintainer="yuanlu"

# ===== 1. 配置 apt 清华源 =====
RUN sed -i 's|http://.*.ubuntu.com|https://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list && \
    sed -i 's|http://security.ubuntu.com|https://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list && \
    sed -i 's|http://packages.ros.org|https://mirrors.tuna.tsinghua.edu.cn/ros|g' /etc/apt/sources.list.d/ros2-latest.list || true && \
    apt-get update

# ===== 2. 安装基础工具 =====
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y \
    sudo \
    build-essential \
    cmake \
    git \
    wget \
    curl \
    python3-pip \
    python3-dev \
    pkg-config \
    && rm -rf /var/lib/apt/lists/*

# ===== 3. 配置 sudo 免密 =====
RUN echo "ALL ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/nopasswd-all && \
    chmod 440 /etc/sudoers.d/nopasswd-all

# ===== 4. 配置 pip3 清华源 =====
RUN pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple && \
    pip3 config set global.trusted-host pypi.tuna.tsinghua.edu.cn

# ===== 5. 创建用户 =====
ARG USER_ID=1000
ARG GROUP_ID=1000
ARG USERNAME=ifr

RUN groupadd -g ${GROUP_ID} ${USERNAME} && \
    useradd -m -u ${USER_ID} -g ${GROUP_ID} -s /bin/bash ${USERNAME}

# ===== 6. OpenCV 4.13.0 + contrib 从 GitHub 拉取并编译安装 =====
# OpenCV 源码和 contrib 模块从 GitHub Release 直接下载
RUN cd /tmp && \
    wget -q -O opencv-4.13.0.zip https://github.com/opencv/opencv/archive/4.13.0.zip && \
    wget -q -O opencv_contrib-4.13.0.zip https://github.com/opencv/opencv_contrib/archive/4.13.0.zip

# 安装 OpenCV 编译依赖（apt 通过清华源联网安装）
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
    libgtk-3-dev \
    libavcodec-dev \
    libavformat-dev \
    libswscale-dev \
    libv4l-dev \
    libxvidcore-dev \
    libx264-dev \
    libdc1394-22-dev \
    libgstreamer-plugins-base1.0-dev \
    libgstreamer1.0-dev \
    libtbb2 \
    libtbb-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# 解压并编译安装 OpenCV
RUN cd /tmp && \
    unzip -q opencv-4.13.0.zip && \
    unzip -q opencv_contrib-4.13.0.zip && \
    mkdir -p opencv-4.13.0/build && cd opencv-4.13.0/build && \
    cmake \
        -D CMAKE_BUILD_TYPE=Release \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D OPENCV_EXTRA_MODULES_PATH=/tmp/opencv_contrib-4.13.0/modules \
        -D BUILD_EXAMPLES=OFF \
        -D BUILD_TESTS=OFF \
        -D BUILD_PERF_TESTS=OFF \
        -D WITH_CUDA=OFF \
        -D WITH_FFMPEG=ON \
        -D WITH_TBB=ON \
        -D WITH_GSTREAMER=ON \
        -D WITH_V4L=ON \
        -D WITH_OPENGL=OFF \
        -D WITH_QT=OFF \
        -D WITH_OPENCL=OFF \
        -D BUILD_opencv_python3=ON \
        -D PYTHON3_EXECUTABLE=$(which python3) \
        -D OPENCV_ENABLE_NONFREE=ON \
        .. && \
    make -j$(nproc) && \
    make install && \
    ldconfig && \
    cd /tmp && rm -rf opencv-4.13.0 opencv_contrib-4.13.0 opencv-4.13.0.zip opencv_contrib-4.13.0.zip

# ===== 7. Eigen3 (在线安装，体积小，走清华源很快) =====
# Eigen3 是纯头文件库，可以直接 apt 安装
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
    libeigen3-dev \
    && rm -rf /var/lib/apt/lists/*

# ===== 8. 工作目录 =====
WORKDIR /home/ifr/ifr-ros2

# 默认以 ifr 用户运行
USER ${USERNAME}

CMD ["bash"]
