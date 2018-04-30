#FROM ubuntu:18.04
FROM ubuntu:16.04

ARG version=1.8.0

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    emacs git wget cmake tk-dev software-properties-common \
    libclang-4.0-dev  llvm-4.0-dev zlib1g-dev libtinfo-dev libhwloc-dev \
    openssl libssl-dev libbz2-dev libreadline-dev libsqlite3-dev \
    libltdl3-dev pkg-config clang-4.0 && \
    rm -rf /var/lib/apt/lists/*

RUN cd && git config --global http.sslVerify false && \
    git clone https://github.com/kuetan/.setting.git && \
    ${HOME}/.setting/install.sh && ${HOME}/.setting/link.sh

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    git \
    libcurl3-dev \
    libfreetype6-dev \
    libhdf5-serial-dev \
    libpng12-dev \
    libzmq3-dev \
    pkg-config \
    rsync \
    software-properties-common \
    unzip \
    zip \
    zlib1g-dev \
    wget \
    && \
    rm -rf /var/lib/apt/lists/*

RUN curl -fSsL -O https://bootstrap.pypa.io/get-pip.py && \
    /root/.pyenv/shims/python3 get-pip.py && \
    rm get-pip.py

ENV PATH $PATH:~/.pyenv/shims
ENV PYTHON_BIN_PATH /root/.pyenv/shims/python
ENV PYTHON_LIB_PATH /root/.pyenv/versions/3.6.5/lib
ENV PYTHONPATH /root/.pyenv/versions/3.6.5/lib/python3.6/site-packages
ENV TF_NEED_S3 0
ENV TF_NEED_KAFKA 0
ENV TF_NEED_GCP 0
ENV TF_NEED_CUDA 0
ENV TF_CUDA_VERSION 9.1
ENV TF_CUDA_COMPUTE_CAPABILITIES 6.1
ENV TF_NEED_HDFS 0
ENV TF_NEED_OPENCL 0
ENV TF_NEED_JEMALLOC 1
ENV TF_ENABLE_XLA 1
ENV TF_NEED_VERBS 0
ENV TF_CUDA_CLANG 0
ENV TF_CUDNN_VERSION 7.0
ENV TF_NEED_MKL 0
ENV TF_DOWNLOAD_MKL 0
ENV TF_NEED_MPI 0
ENV TF_NCCL_VERSION 1.3
ENV GCC_HOST_COMPILER_PATH /usr/bin/gcc
ENV CC_OPT_FLAGS "-march=native"

# =============================================================
# BUILD NEW VERSION
# =============================================================

RUN cd && wget https://github.com/bazelbuild/bazel/releases/download/0.12.0/bazel-0.12.0-installer-linux-x86_64.sh && \
    apt install -y pkg-config zip g++ zlib1g-dev && \
    chmod +x bazel-0.12.0-installer-linux-x86_64.sh &&\
    ./bazel-0.12.0-installer-linux-x86_64.sh --user

ENV PATH $PATH:/root/bin

RUN apt update && apt install -y libprotobuf-dev protobuf-compiler && \
    ${HOME}/.pyenv/shims/pip install six numpy wheel && \
    wget https://github.com/tensorflow/tensorflow/archive/v${version}.tar.gz -P /opt/ && \
    cd /opt/ && tar zxvf v${version}.tar.gz && \
    cd /opt/tensorflow-${version} && bazel clean && \
    ln -s /root/.pyenv/shims/python3 /usr/bin/python && \
    ./configure && \
    bazel build --action_env="LD_LIBRARY_PATH=:${LD_LIBRARY_PATH}" --config=opt  //tensorflow/tools/pip_package:build_pip_package && \
    bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg && \
    ${HOME}/.pyenv/shims/pip install /tmp/tensorflow_pkg/tensorflow-${version}-cp36-cp36m-linux_x86_64.whl
