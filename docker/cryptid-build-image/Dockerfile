FROM i386/ubuntu:16.04

SHELL ["/bin/bash", "-c"]
ENV BASE_PATH /usr/dependencies
WORKDIR ${BASE_PATH}


# --- Build arguments

ARG GMP_VERSION=6.1.2
ARG EMSDK_VERSION=1.38.31
ARG BUILD_DATE
ARG VCS_REF

# ---


# --- Labels

LABEL maintainer="attila.bagossy@beardybytes.com" \
      org.label-schema.name="cryptid-build-image" \
      org.label-schema.description="The CryptID build image includes a 32 bit emscripten version along with GMP and Node.js." \
      org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.vcs-url="https://github.com/cryptid-org/cryptid-build-image" \
      org.label-schema.docker.dockerfile="/docker/cryptid-build-image/Dockerfile"

# ---


# --- Environment variables

ENV EMSDK_TAG sdk-tag-${EMSDK_VERSION}-32bit
ENV GMP_DEBUG_OUTPUT_PREFIX=${BASE_PATH}/binaries/gmp/debug
ENV GMP_RELEASE_OUTPUT_PREFIX=${BASE_PATH}/binaries/gmp/release

# ---


# --- Install necessary tools

RUN apt-get update -y \
 && apt-get install -y --no-install-recommends \
        autoconf \
        build-essential \
        ca-certificates \
        cmake \
        git \
        lcov \
        lzip \
        mawk \
        python2.7 \
        wget \
 && update-ca-certificates \
 && ln -s /usr/bin/python2.7 \
          /usr/bin/python

# ---


# --- Get GMP

RUN wget https://gmplib.org/download/gmp/gmp-${GMP_VERSION}.tar.lz -q \
 && tar xf gmp-${GMP_VERSION}.tar.lz \
 && rm -rf gmp-${GMP_VERSION}.tar.lz

# ---


# --- Build GMP

# --- --- First build a LOCAL version, which will be used for NATIVE

RUN cd gmp-${GMP_VERSION} \
 && ./configure \
        --disable-assembly \
        --disable-shared \
        --build i386-pc-linux-gnu \
        CFLAGS=-g \
 && make \
 && make install \
 && make clean \

# --- ---

# ---


# --- Get and activate emscripten
# --- --- Build a GMP EMCC RELEASE version with optimizations enabled
# --- --- Build a GMP EMCC DEBUG version with debug symbols enabled
# Must be done in the same RUN command so that source holds effect.

RUN git clone https://github.com/emscripten-core/emsdk \
        emsdk \
 && cd emsdk \
 && ./emsdk install ${EMSDK_TAG} \
 && ln -s ${BASE_PATH}/emsdk/binaryen/tag-${EMSDK_VERSION}_32bit_binaryen \
          ${BASE_PATH}/emsdk/binaryen/tag-${EMSDK_VERSION}_64bit_binaryen \
 && ./emsdk activate ${EMSDK_TAG} \
 && source ./emsdk_env.sh \
 && cd ${BASE_PATH} \
 && cd gmp-${GMP_VERSION} \
 && emconfigure ./configure \
        --disable-assembly \
        --disable-shared \
        --build i386-pc-linux-gnu \
        --prefix=${GMP_RELEASE_OUTPUT_PREFIX} \
        --host none \
        CFLAGS=-O3 \
 && make \
 && make install \
 && make clean \
 && emconfigure ./configure \
        --disable-assembly \
        --disable-shared \
        --build i386-pc-linux-gnu \
        --prefix=${GMP_DEBUG_OUTPUT_PREFIX} \
        --host none \
        CFLAGS=-g \
 && make \
 && make install \
 && make clean \

# ---
