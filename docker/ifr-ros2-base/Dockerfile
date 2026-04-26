# 基础镜像
FROM ros:foxy-ros-base

LABEL maintainer="yuanlu"

# ===== 配置 apt 清华源 =====
RUN sed -i 's|http://.*.ubuntu.com|https://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list && \
    sed -i 's|http://security.ubuntu.com|https://mirrors.tuna.tsinghua.edu.cn|g' /etc/apt/sources.list && \
    (test -f /etc/apt/sources.list.d/ros2-latest.list && sed -i 's|http://packages.ros.org|https://mirrors.tuna.tsinghua.edu.cn/ros|g' /etc/apt/sources.list.d/ros2-latest.list || true) && \
    apt-get update

# ===== 安装基础工具 =====
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

# ===== 配置 sudo 免密 =====
RUN echo "ALL ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/nopasswd-all && \
    chmod 440 /etc/sudoers.d/nopasswd-all

# ===== 配置 pip3 清华源 =====
RUN pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple && \
    pip3 config set global.trusted-host pypi.tuna.tsinghua.edu.cn

# ===== 创建用户 =====
ARG USER_ID=1000
ARG GROUP_ID=1000
ARG USERNAME=ifr

RUN groupadd -g ${GROUP_ID} ${USERNAME} && \
    useradd -m -u ${USER_ID} -g ${GROUP_ID} -s /bin/bash ${USERNAME}




# 安装 OpenCV 编译依赖
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
    libeigen3-dev \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# ===== 安装 GCC 13.2.0 =====
# 添加 Ubuntu Toolchain PPA（提供最新 GCC）
RUN DEBIAN_FRONTEND=noninteractive apt-get update && \
    apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:ubuntu-toolchain-r/test && \
    apt-get update && \
    apt-get install -y gcc-13 g++-13 && \
    update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-13 100 && \
    update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-13 100 && \
    update-alternatives --install /usr/bin/cc cc /usr/bin/gcc-13 100 && \
    update-alternatives --install /usr/bin/c++ c++ /usr/bin/g++-13 100 && \
    rm -rf /var/lib/apt/lists/*


# ===== 安装 Clangd 17.0.3 =====
# 下载 LLVM 官方为 Ubuntu 20.04 预编译的二进制包
RUN cd /tmp && \
    wget -q https://github.com/llvm/llvm-project/releases/download/llvmorg-17.0.3/clang+llvm-17.0.3-x86_64-linux-gnu-ubuntu-20.04.tar.xz && \
    tar -xf clang+llvm-17.0.3-x86_64-linux-gnu-ubuntu-20.04.tar.xz && \
    # 移动至 /opt 目录并建立软链接
    mv clang+llvm-17.0.3-x86_64-linux-gnu-ubuntu-20.04 /opt/llvm-17.0.3 && \
    update-alternatives --install /usr/bin/clangd clangd /opt/llvm-17.0.3/bin/clangd 100 && \
    rm -f clang+llvm-17.0.3-x86_64-linux-gnu-ubuntu-20.04.tar.xz

# ===== OpenCV 4.13.0 + contrib =====
RUN cd /tmp && \
    wget -q -O opencv-4.13.0.zip https://github.com/opencv/opencv/archive/4.13.0.zip && \
    wget -q -O opencv_contrib-4.13.0.zip https://github.com/opencv/opencv_contrib/archive/4.13.0.zip && \
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


# ===== 安装 ROS / 系统依赖包 =====
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
    libzmq3-dev \
    libgraphicsmagick++1-dev \
    libompl-dev \
    libceres-dev \
    ros-foxy-gazebo-ros-pkgs \
    ros-foxy-octomap-msgs \
    ros-foxy-test-msgs \
    ros-foxy-behaviortree-cpp-v3 \
    ros-foxy-pcl-ros \
    ros-foxy-octomap-ros \
    ros-foxy-nmea-msgs \
    ros-foxy-geographic-msgs \
    ros-foxy-diagnostic-updater \
    ros-foxy-localization \
    && rm -rf /var/lib/apt/lists/*


# ===== 工作目录 =====
WORKDIR /home/ifr/ifr-ros2

USER ${USERNAME}

CMD ["bash"]
