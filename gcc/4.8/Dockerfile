FROM bscstorage/gcc-toolchain:4.8
MAINTAINER alberto.miranda@bsc.es

# install dependencies from package manager
RUN set -eux && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
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
        libprotobuf-c0-dev \
        protobuf-c-compiler \
        libyaml-cpp-dev \
        libyaml-dev \
        libarchive-dev \
        libmpich-dev \
        make \
        cmake \
        lcov && \
    update-alternatives --quiet --install /usr/bin/cpp cpp /usr/bin/cpp-4.8 20 && \
    update-alternatives --quiet --install /usr/bin/gcc gcc /usr/bin/gcc-4.8 20 && \
    update-alternatives --quiet --install /usr/bin/g++ g++ /usr/bin/g++-4.8 20 && \
    update-alternatives --quiet --install /usr/bin/gcov gcov /usr/bin/gcov-4.8 20 && \
    rm -rf /var/lib/apt/lists/*

# libprotobuf-c0-dev unfortunately doesn't provide a pkgconfig file
COPY libprotobuf-c.pc /usr/lib/x86_64-linux-gnu/pkgconfig/

# install dependencies not on package manager

# Allow build-time overrides for libfabric
# Options for LIBFABRIC_VERSION: a valid tag
#   Example: docker build --build-arg LIBFABRIC_VERSION=v1.7.1 
ARG LIBFABRIC_VERSION=v1.7.1
ENV LIBFABRIC_VERSION=${LIBFABRIC_VERSION}

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

# Allow build-time overrides for mercury
# Options for MERCURY_VERSION: a valid tag
#   Example: docker build --build-arg MERCURY_VERSION=v1.0.1
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
        -DOPA_INCLUDE_DIR:FILEPATH=/usr/include/mpich \
        -DOPA_LIBRARY:FILEPATH=/usr/lib/x86_64-linux-gnu/libopa.so \
        .. && \
    make && \
    make install && \
    ldconfig && \
    cd ../../ && \
    rm -rf mercury

CMD [ "/bin/bash" ]
