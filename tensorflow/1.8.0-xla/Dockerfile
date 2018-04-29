FROM nvidia/cuda:9.1-cudnn7-devel-ubuntu16.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    emacs git wget cmake tk-dev software-properties-common \
    libclang-4.0-dev  llvm-4.0-dev zlib1g-dev libtinfo-dev libhwloc-dev \
    openssl libssl-dev libbz2-dev libreadline-dev libsqlite3-dev \
    libltdl3-dev pkg-config clang-4.0 && \
    rm -rf /var/lib/apt/lists/*

RUN cd && git config --global http.sslVerify false && \
    git clone https://github.com/kuetan/.setting.git && \
    $HOME/.setting/install.sh && $HOME/.setting/link.sh

#ENV PATH $PATH:~/.pyenv/shims
ENV PYTHON_BIN_PATH /root/.pyenv/shims/python
ENV PYTHON_LIB_PATH /root/.pyenv/versions/3.6.5/lib
ENV PYTHONPATH /root/.pyenv/versions/3.6.5/lib/python3.6/site-packages
#ENV PYTHON_ARG /opt/tensorflow-1.8.0/lib
ENV CUDA_TOOLKIT_PATH /usr/local/cuda
ENV CUDA_PATH /usr/local/cuda
ENV CUDA_PATH_LINUX /usr/local/cuda
ENV CUDNN_INSTALL_PATH /usr/local/cuda
ENV TF_NEED_S3 0
ENV TF_NEED_KAFKA 0
ENV TF_NEED_GCP 0
ENV TF_NEED_CUDA 1
ENV TF_CUDA_VERSION 9.1
ENV TF_CUDA_COMPUTE_CAPABILITIES 6.1
ENV TF_NEED_HDFS 0
ENV TF_NEED_OPENCL 0
ENV TF_NEED_JEMALLOC 0
ENV TF_ENABLE_XLA 0
ENV TF_NEED_VERBS 0
ENV TF_CUDA_CLANG 0
ENV TF_CUDNN_VERSION 7.0
ENV TF_NEED_MKL 0
ENV TF_DOWNLOAD_MKL 0
ENV TF_NEED_MPI 0
ENV TF_NCCL_VERSION 1.3
ENV GCC_HOST_COMPILER_PATH /usr/bin/gcc
ENV CC_OPT_FLAGS "-march=native"
ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64
ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64


# =============================================================
# BUILD NEW VERSION
# =============================================================
RUN apt install -y openjdk-8-jdk curl && add-apt-repository ppa:webupd8team/java -y &&\
    echo debconf shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
    echo debconf shared/accepted-oracle-license-v1-1 seen true | debconf-set-selections && \
    apt-get update && apt-get install -y oracle-java8-installer && \
    echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | tee /etc/apt/sources.list.d/bazel.list && \
    curl https://bazel.build/bazel-release.pub.gpg | apt-key add - && \
    apt-get update && apt-get install -y bazel && \
    apt-get -y upgrade bazel && \
    apt install -y python3 python3-dev python3-pip libprotobuf-dev protobuf-compiler && \
    $HOME/.pyenv/shims/pip install six numpy wheel && \
    wget https://github.com/tensorflow/tensorflow/archive/v1.8.0.tar.gz -P /opt/ && \
    cd /opt/ && tar zxvf v1.8.0.tar.gz && \
    cd /opt/tensorflow-1.8.0 && bazel clean && \
    ln -s /root/.pyenv/shims/python3 /usr/bin/python && \
    ./configure && \
    bazel build  --config=opt  --config=cuda //tensorflow/tools/pip_package:build_pip_package && \
    bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg && \
    $HOME/.pyenv/shims/pip install /tmp/tensorflow_pkg/tensorflow-1.8.0-cp36-cp36m-linux_x86_64.whl
