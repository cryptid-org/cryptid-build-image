FROM i386/ubuntu:16.04


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
ENV GMP_DEBUG_OUTPUT_PREFIX=${HOME}/binaries/gmp/debug
ENV GMP_RELEASE_OUTPUT_PREFIX=${HOME}/binaries/gmp/release

# ---


# --- Install necessary tools

RUN apt-get update -y \
 && apt-get install -y --no-install-recommends \
        autoconf \
        build-essential \
        ca-certificates \
        cmake \
        git \
        lzip \
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
 && cd

# --- ---

# ---


# --- Get and activate emscripten

RUN git clone https://github.com/emscripten-core/emsdk \
        emsdk \
 && cd emsdk \
 && ./emsdk install ${EMSDK_TAG} \
 && ln -s ${HOME}/emsdk/binaryen/tag-${EMSDK_VERSION}_32bit_binaryen \
          ${HOME}/emsdk/binaryen/tag-${EMSDK_VERSION}_64bit_binaryen \
 && ./emsdk activate ${EMSDK_TAG} \
 && source ./emsdk_env.sh \
 && cd

# ---


# --- Build GMP (contd.)

# --- --- Build an EMCC RELEASE version with optimizations enabled

RUN cd gmp-${GMP_VERSION} \
 && emconfigure ./configure \
        --disable-assembly \
        --disable-shared \
        --build i386-pc-linux-gnu \
        --prefix=${GMP_RELEASE_OUTPUT_PREFIX} \
        --host none \
        CFLAGS=-O3 \
 && make \
 && make install \
 && make clean

# --- ---

# --- --- Build an EMCC DEBUG version with debug symbols enabled

RUN emconfigure ./configure \
        --disable-assembly \
        --disable-shared \
        --build i386-pc-linux-gnu \
        --prefix=${GMP_DEBUG_OUTPUT_PREFIX} \
        --host none \
        CFLAGS=-g \
 && make \
 && make install \
 && make clean \
 && cd

# --- ---

# ---
