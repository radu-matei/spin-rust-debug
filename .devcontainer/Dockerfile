FROM rust:1.99.1-bookworm

ARG TARGETARCH
ARG SPIN_VERSION=4.3.0

RUN apt-get update \
    && apt-get install -y --no-install-recommends procps python3 \
    && rustup target add wasm32-wasip2 \
    && rustup component add rust-src \
    && useradd --create-home --shell /bin/bash vscode

RUN set -eux; \
    case "${TARGETARCH}" in \
        amd64) spin_arch=amd64 ;; \
        arm64) spin_arch=aarch64 ;; \
        *) echo "Unsupported architecture: ${TARGETARCH}" >&2; exit 1 ;; \
    esac; \
    archive="spin-v${SPIN_VERSION}-linux-${spin_arch}.tar.gz"; \
    base_url="https://github.com/spinframework/spin/releases/download/v${SPIN_VERSION}"; \
    mkdir /usr/local/src/spin-download; \
    cd /usr/local/src/spin-download; \
    curl -fsSLo "${archive}" "${base_url}/${archive}"; \
    curl -fsSLo spin-checksums.txt "${base_url}/checksums-v${SPIN_VERSION}.txt"; \
    grep " ${archive}$" spin-checksums.txt | sha256sum -c -; \
    tar -xzf "${archive}" -C /usr/local/bin spin; \
    rm "${archive}" spin-checksums.txt; \
    cd /; \
    rmdir /usr/local/src/spin-download; \
    spin --version

ENV CARGO_HOME=/home/vscode/.cargo
USER vscode
