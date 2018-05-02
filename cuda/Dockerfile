ARG repository
FROM ubuntu:18.04

RUN apt-get update  && apt upgrade &&\
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    nvidia-driver-390 nvidia-cuda-toolkit wget &&\
    rm -rf /var/lib/apt/lists/*

RUN wget http://developer.download.nvidia.com/compute/redist/cudnn/v7.1.3/cudnn-9.1-linux-x64-v7.1.tgz && \
    tar xzf cudnn-9.1-linux-x64-v7.1.tgz && \
    cp -a cuda/lib64/* /usr/local/lib/ && \
    cp -a cuda/include/* /usr/local/include/ && \
    ldconfig && rm -R -f cuda cudnn-9.1-linux-x64-v7.1.tgz

ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs:${LIBRARY_PATH}
