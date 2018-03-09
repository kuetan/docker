FROM nvidia/cuda:9.1-devel-ubuntu16.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    emacs fish git wget cmake \
    libclang-4.0-dev  llvm-4.0-dev zlib1g-dev libtinfo-dev libhwloc-dev \ 
    libltdl3-dev pkg-config clang-4.0 && \
    rm -rf /var/lib/apt/lists/*

RUN chsh -s /usr/bin/fish && \
    git config --global http.sslVerify false && \
    git clone https://github.com/kuetan/.emacs.d.git
			    
RUN cd /opt && \
    wget --no-check-certificate https://github.com/pocl/pocl/archive/v1.0.tar.gz &&\
    tar zxvf v1.0.tar.gz