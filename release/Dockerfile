ARG GO_VERSION=1.16
FROM golang:$GO_VERSION
SHELL ["/bin/bash", "-c"]
RUN apt-get update && apt-get install -y unzip

ARG GORELEASER_URL=https://github.com/goreleaser/goreleaser/releases/download/v0.168.0/goreleaser_Linux_x86_64.tar.gz
ARG GORELEASER_SHA256=6611e8fc645671b002af8c646eae9777a1daa68682e0c30475e0a689c2dbad2f
WORKDIR /go/goreleaser
RUN set -x && \
    GORELEASER=$(basename $GORELEASER_URL) && \
    curl -L $GORELEASER_URL > ./$GORELEASER && \
    sha256sum -c <(echo "$GORELEASER_SHA256 $GORELEASER") && \
    tar xfzv $GORELEASER && \
    mv goreleaser /usr/local/bin

ARG VAULT_URL=https://releases.hashicorp.com/vault/1.5.4/vault_1.5.4_linux_amd64.zip
ARG VAULT_SHA256=50156e687b25b253a63c83b649184c79a1311f862c36f4ba16fd020ece4ed3b3
ARG VAULT_GPG_KEY=72D7468F
ARG VAULT_GPG_FINGERPRINT=C874011F0AB405110D02105534365D9472D7468F
ARG VAULT_SUMFILE_URL=https://releases.hashicorp.com/vault/1.5.4/vault_1.5.4_SHA256SUMS
ARG VAULT_SUMFILE_SIG_URL=https://releases.hashicorp.com/vault/1.5.4/vault_1.5.4_SHA256SUMS.sig
WORKDIR /go/vault
RUN set -x && \
    VAULT=$(basename $VAULT_URL) && \
    VAULT_SUMFILE=$(basename $VAULT_SUMFILE_URL) && \
    VAULT_SUMFILE_SIG=$(basename $VAULT_SUMFILE_SIG_URL) && \
    curl -L $VAULT_URL > $VAULT && \
    curl -L $VAULT_SUMFILE_URL > $VAULT_SUMFILE && \
    curl -L $VAULT_SUMFILE_SIG_URL > $VAULT_SUMFILE_SIG && \
    rm -rf ~/.gnupg && \
    gpg --receive-keys --keyserver ipv4.pool.sks-keyservers.net $VAULT_GPG_KEY && \
    gpg --list-keys && \
    gpg --check-signatures $VAULT_GPG_FINGERPRINT && \
    gpg --verify $VAULT_SUMFILE_SIG  $VAULT_SUMFILE && \
    sha256sum -c <(grep $VAULT_SHA256 $VAULT_SUMFILE) && \
    unzip $VAULT && \
    mv vault /usr/local/bin

ADD entrypoint.sh /
ENV VAULT_ADDR="" VAULT_LOGIN="" VAULT_LOGIN_PASSWORD="" VAULT_SIGNER_PATH=""
ENV GITHUB_TOKEN="" GITHUB_OWNER=""

WORKDIR /go/src
ENTRYPOINT ["/entrypoint.sh"]