FROM openjdk:8-jre-slim AS build

ARG ANT_VERSION=1.10.12
ARG ANT_HOME=/opt/ant
ARG SENCHACMD_VERSION=6.2.0.103
ARG OPENSSL_VERSION=1.1.1k

# Install dependencies including tools to build OpenSSL
RUN apt-get update && apt-get install -y --no-install-recommends \
        wget \
        unzip \
        libfreetype6 \
        fontconfig \
        ruby-full \
        build-essential \
        zlib1g-dev \
        libssl-dev \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Download and install OpenSSL from source
RUN wget https://www.openssl.org/source/openssl-${OPENSSL_VERSION}.tar.gz \
    && tar -zxf openssl-${OPENSSL_VERSION}.tar.gz \
    && cd openssl-${OPENSSL_VERSION} \
    && ./config --prefix=/usr/local/ssl --openssldir=/usr/local/ssl shared zlib \
    && make \
    && make install \
    && cd .. \
    && rm -rf openssl-${OPENSSL_VERSION} \
    && rm openssl-${OPENSSL_VERSION}.tar.gz


# Verify OpenSSL installation and library paths
RUN openssl version -a && ls -l /usr/local/ssl/lib/libssl*

# Download and extract Apache Ant to opt folder
RUN wget --no-check-certificate --no-cookies http://archive.apache.org/dist/ant/binaries/apache-ant-${ANT_VERSION}-bin.tar.gz \
    && wget --no-check-certificate --no-cookies http://archive.apache.org/dist/ant/binaries/apache-ant-${ANT_VERSION}-bin.tar.gz.sha512 \
    && echo "$(cat apache-ant-${ANT_VERSION}-bin.tar.gz.sha512) apache-ant-${ANT_VERSION}-bin.tar.gz" | sha512sum -c \
    && tar -zvxf apache-ant-${ANT_VERSION}-bin.tar.gz -C /opt/ \
    && ln -s /opt/apache-ant-${ANT_VERSION} /opt/ant \
    && unlink apache-ant-${ANT_VERSION}-bin.tar.gz \
    && unlink apache-ant-${ANT_VERSION}-bin.tar.gz.sha512

# Installing SenchaCmd
RUN wget --no-check-certificate --no-cookies http://cdn.sencha.com/cmd/${SENCHACMD_VERSION}/no-jre/SenchaCmd-${SENCHACMD_VERSION}-linux-amd64.sh.zip \
    && unzip SenchaCmd-${SENCHACMD_VERSION}-linux-amd64.sh.zip -d /tmp \
    && unlink SenchaCmd-${SENCHACMD_VERSION}-linux-amd64.sh.zip \
    && chmod o+x /tmp/SenchaCmd-${SENCHACMD_VERSION}*-linux-amd64.sh \
    && /tmp/SenchaCmd-${SENCHACMD_VERSION}*-linux-amd64.sh -Dall=true -q -dir /opt/Sencha/Cmd/${SENCHACMD_VERSION} \
    && unlink /tmp/SenchaCmd-${SENCHACMD_VERSION}*-linux-amd64.sh

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends curl ca-certificates; \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Set environment variables
ENV LD_LIBRARY_PATH="/usr/local/ssl/lib"
ENV PKG_CONFIG_PATH="/usr/local/ssl/lib/pkgconfig"
ENV OPENSSL_CONF="/usr/local/ssl/ssl/openssl.cnf"
ENV PATH="/opt/ant/bin:/opt/Sencha/Cmd:/usr/local/ssl/bin:${PATH}"
