FROM alpine:latest
MAINTAINER Damir Kadyrov <damirikadyrov@gmail.com>

ARG HBASE_VERSION=1.4.9

ENV PATH $PATH:/hbase/bin

ENV JAVA_HOME=/usr

LABEL Description="HBase Dev"
LABEL HBaseVersion="$HBASE_VERSION"

WORKDIR /

RUN set -euxo pipefail && \
    apk add --no-cache bash openjdk8-jre-base

RUN set -euxo pipefail && \
    apk add --no-cache wget tar libexecinfo libexecinfo-dev snappy && \
    url="http://www.apache.org/dyn/closer.lua?filename=hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz&action=download"; \
    url_archive="http://archive.apache.org/dist/hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz"; \
    wget -t 10 --max-redirect 1 --retry-connrefused -O "hbase-$HBASE_VERSION-bin.tar.gz" "$url" || \
    wget -t 10 --max-redirect 1 --retry-connrefused -O "hbase-$HBASE_VERSION-bin.tar.gz" "$url_archive" && \
    mkdir "hbase-$HBASE_VERSION" && \
    tar zxf "hbase-$HBASE_VERSION-bin.tar.gz" -C "hbase-$HBASE_VERSION" --strip 1 && \
    test -d "hbase-$HBASE_VERSION" && \
    ln -sv "hbase-$HBASE_VERSION" hbase && \
    rm -fv "hbase-$HBASE_VERSION-bin.tar.gz" && \
    rm -rf hbase-$HBASE_VERSION/docs && \
    rm -rf hbase-$HBASE_VERSION/src && \
    mkdir /data && \
    apk del tar wget

RUN set -euxo pipefail && \
    wget -q "https://github.com/dizzarg/hbase-local-docker/raw/master/libhadoop.so" && \
    mkdir /hadoop-native && \
    mv libhadoop.so /hadoop-native

ENV HBASE_DATA_DIR=/data
ENV HBASE_LOG_DIR=$HBASE_DATA_DIR
ENV ZOOKEEPER_QUORUM=zookeeper-quorum:2181
ENV MASTER_HOSTNAME=master-hostname
ENV MASTER_PORT=16000
ENV REGIONSERVER_PORT=16030
ENV REGIONSERVERS=regionserver1,regionserver2    
ENV LD_LIBRARY_PATH=/hadoop-native:/usr/lib:/usr/local/lib
ENV HBASE_ROLE=master

VOLUME ["/data"]

WORKDIR /hbase-$HBASE_VERSION

COPY docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]

