# First stage is the build environment
FROM sgrio/java-oracle:jdk_8 as builder
MAINTAINER Jordan Halterman <jordan@opennetworking.org>

# Set the environment variables
ENV HOME /root
ENV JAVA_TOOL_OPTIONS=-Dfile.encoding=UTF8

# Get arguments
ARG REPO=https://github.com/atomix/atomix.git
ARG BRANCH=master

# Change to the build directory
WORKDIR /src/atomix
RUN apt-get update && apt-get install -y zip maven git bzip2 build-essential \
    && git clone --branch $BRANCH $REPO /src/atomix \
    && cd /src/atomix \
    && ln -s /usr/lib/jvm/java-8-oracle/bin/jar /etc/alternatives/jar \
    && ln -s /etc/alternatives/jar /usr/bin/jar \
    && mvn package -DskipTests -Ddockerfile.skip \
    && mkdir -p /src/tar \
    && cd /src/tar \
    && tar -xf /src/atomix/dist/target/atomix.tar.gz \
    && rm -rf .git

# Second stage is the runtime environment
FROM anapsix/alpine-java:8_server-jre

ENV ATOMIX_DATA_DIR=/var/lib/atomix/data
ENV ATOMIX_LOG_DIR=/var/log/atomix

# Change to /root directory
RUN apk update \
    && mkdir -p /root/atomix
WORKDIR /root/atomix

# Install Atomix
COPY --from=builder /src/tar/ .

# Configure Atomix to log to stdout
COPY conf/logback.xml conf/logback.xml

COPY scripts /root/atomix/bin/

# Ports
# 5678 - Atomix REST API
# 5679 - Atomix intra-cluster communication
EXPOSE 5678 5679

RUN set -x \
  && mkdir -p $ATOMIX_DATA_DIR $ATOMIX_LOG_DIR \
