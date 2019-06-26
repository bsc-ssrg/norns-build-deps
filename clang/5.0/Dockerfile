FROM bscstorage/clang-toolchain:5.0
MAINTAINER alberto.miranda@bsc.es

# install dependencies from package manager
RUN set -eux && \
    apt-get update && \
    apt-get install -y \
        git \
        automake \
        autoconf \
        libtool \
        pkg-config \
        libboost-system-dev \
        libboost-filesystem-dev \
        libboost-program-options-dev \
        libboost-thread-dev \
        libboost-regex-dev \
        libprotobuf-dev \
        protobuf-compiler \
        libprotobuf-c-dev \
        protobuf-c-compiler \
        libyaml-cpp-dev \
        libyaml-dev \
        libarchive-dev \
        cmake && \
    rm -rf /var/lib/apt/lists/*

ENV CC=clang
ENV CXX=clang++
ENV CPP="clang -E"

# Allow build-time overrides
# Options for LIBFABRIC_VERSION: a valid tag
# Example: docker build --build-arg LIBFABRIC_VERSION=v1.7.1 
ARG LIBFABRIC_VERSION=v1.7.1
ENV LIBFABRIC_VERSION=${LIBFABRIC_VERSION}

# install dependencies not on package manager
RUN set -eux && \
    git clone https://github.com/ofiwg/libfabric.git && \
    cd libfabric && \
    git reset --hard ${LIBFABRIC_VERSION} && \
    ./autogen.sh && \
    mkdir build && \
    cd build && \
    ../configure && \
    make && \
    make install && \
    ldconfig && \
    cd ../../ && \
    rm -rf libfabric

# Allow build-time overrides
# Options for MERCURY_VERSION: a valid tag
# Example: docker build --build-arg MERCURY_VERSION=v1.0.1
ARG MERCURY_VERSION=v1.0.1
ENV MERCURY_VERSION=${MERCURY_VERSION}

RUN set -eux && \
    git clone https://github.com/mercury-hpc/mercury.git && \
    cd mercury && \
    git reset --hard ${MERCURY_VERSION} && \
    mkdir build && \
    cd build && \
    cmake \
        -DCMAKE_BUILD_TYPE:STRING=Release \
        -DBUILD_TESTING:BOOL=OFF \
        -DMERCURY_USE_SM_ROUTING:BOOL=OFF \
        -DMERCURY_USE_SELF_FORWARD:BOOL=OFF \
        -DMERCURY_USE_CHECKSUMS:BOOL=OFF \
        -DMERCURY_USE_BOOST_PP:BOOL=ON \
        -DMERCURY_USE_EAGER_BULK:BOOL=ON \
        -DBUILD_SHARED_LIBS:BOOL=ON \
        -DNA_USE_OFI:BOOL=ON \
        .. && \
    make && \
    make install && \
    ldconfig && \
    cd ../../ && \
    rm -rf mercury

CMD [ "/bin/bash" ]
