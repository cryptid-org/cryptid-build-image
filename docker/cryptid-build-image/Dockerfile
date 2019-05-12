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
