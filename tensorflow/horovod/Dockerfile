FROM ubuntu:16.04

# TensorFlow version is tightly coupled to CUDA and cuDNN so it should be selected carefully
ENV TENSORFLOW_VERSION=1.8.0
#ENV CUDNN_VERSION=7.0.5.15-1+cuda9.0
#ENV NCCL_VERSION=2.1.15-1+cuda9.0

# Python 2.7 or 3.5 is supported by Ubuntu Xenial out of the box
ENV PYTHON_VERSION=3.5

#RUN echo "deb http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        curl \
        emacs \
        wget \
        ca-certificates \
        libjpeg-dev \
        libpng-dev \
        sudo \
        python$PYTHON_VERSION \
        python$PYTHON_VERSION-dev

RUN apt install -y software-properties-common && \
    apt remove -y gcc g++ && \
    add-apt-repository ppa:jonathonf/gcc-7.1 -y && \
    apt update && apt install -y gcc-7 g++-7 && \
    ln -s /usr/bin/gcc-7 /usr/bin/gcc  && \
    ln -s /usr/bin/g++-7 /usr/bin/g++  && \
    gcc --version


RUN ln -s /usr/bin/python$PYTHON_VERSION /usr/bin/python

RUN curl -O https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    rm get-pip.py

# Install TensorFlow and Keras
RUN pip install --no-cache-dir tensorflow==$TENSORFLOW_VERSION keras h5py

# Install Open MPI
RUN mkdir /tmp/openmpi && \
    cd /tmp/openmpi && \
    wget https://www.open-mpi.org/software/ompi/v3.0/downloads/openmpi-3.0.0.tar.gz && \
    tar zxf openmpi-3.0.0.tar.gz && \
    cd openmpi-3.0.0 && \
    ./configure --enable-orterun-prefix-by-default && \
    make -j $(nproc) all && \
    make install && \
    ldconfig && \
    rm -rf /tmp/openmpi

# Install Horovod, temporarily using CUDA stubs
#RUN apt install -y build-essential && pip install horovod && \
#    ldconfig
RUN pip install --no-cache-dir horovod && \
    ldconfig

# Create a wrapper for OpenMPI to allow running as root by default
RUN mv /usr/local/bin/mpirun /usr/local/bin/mpirun.real && \
    echo '#!/bin/bash' > /usr/local/bin/mpirun && \
    echo 'mpirun.real --allow-run-as-root "$@"' >> /usr/local/bin/mpirun && \
    chmod a+x /usr/local/bin/mpirun

# Configure OpenMPI to run good defaults:
#   --bind-to none --map-by slot --mca btl_tcp_if_exclude lo,docker0
RUN echo "hwloc_base_binding_policy = none" >> /usr/local/etc/openmpi-mca-params.conf && \
    echo "rmaps_base_mapping_policy = slot" >> /usr/local/etc/openmpi-mca-params.conf && \
    echo "btl_tcp_if_exclude = lo,docker0" >> /usr/local/etc/openmpi-mca-params.conf

# Set default NCCL parameters
RUN echo NCCL_DEBUG=INFO >> /etc/nccl.conf && \
    echo NCCL_SOCKET_IFNAME=^docker0 >> /etc/nccl.conf

# Install OpenSSH for MPI to communicate between containers
RUN apt-get install -y --no-install-recommends openssh-client openssh-server && \
    mkdir -p /var/run/sshd

# Allow OpenSSH to talk to containers without asking for confirmation
RUN cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new && \
    echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new && \
    mv /etc/ssh/ssh_config.new /etc/ssh/ssh_config

# Download examples
RUN cd && git clone https://github.com/uber/horovod.git

# user
RUN groupadd -g 1000 walter seradd -u 1000 -m walter &&  gpasswd -a walter sudo && \
    echo 'walter\nwalter' | passwd

USER walter