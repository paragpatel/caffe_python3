FROM ubuntu:22.04
ENV DEBIAN_FRONTEND noninteractive
#ref: https://github.com/mapler/dockerfiles/blob/master/caffe-py3-cpu/Dockerfile
#https://github.com/paragpatel/caffe
#
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libopencv-dev \
        libprotobuf-dev \
        libsnappy-dev \
        protobuf-compiler \
        python3-dev \
        python3-numpy \
        python3-pip \
        python3-setuptools \
        python3-scipy \
        python-is-python3 && \
    rm -rf /var/lib/apt/lists/*

ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT

# FIXME: use ARG instead of ENV once DockerHub supports this
# https://github.com/docker/hub-feedback/issues/460
ENV CLONE_TAG=https://github.com/BVLC/caffe/tree/9b891540183ddc834a02b2bd81b31afae71b2153

RUN git clone https://github.com/BVLC/caffe.git .
COPY caffe.patch caffe.patch
RUN git apply caffe.patch
RUN pip install --upgrade pip && \
    cd python && for req in $(cat requirements.txt) pydot 'python-dateutil>2'; do pip3 install $req; done && cd .. && \
    mkdir build && cd build && \
    cmake -DCPU_ONLY=1 -Dpython_version=3 .. && \
    make -j"$(nproc)"

ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig
RUN pip uninstall -y protobuf
RUN pip install --no-binary protobuf protobuf
WORKDIR /workspace
