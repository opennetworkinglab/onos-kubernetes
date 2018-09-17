# First stage is the build environment
FROM sgrio/java-oracle:jdk_8 as builder
MAINTAINER Jordan Halterman <jordan@opennetworking.org>

# Set the environment variables
ENV HOME /root
ENV BUILD_NUMBER docker
ENV JAVA_TOOL_OPTIONS=-Dfile.encoding=UTF8

# Change to the build directory
WORKDIR /src/onos
RUN apt-get update && apt-get install -y zip python git bzip2 build-essential \
    && git clone --branch onos-proxy https://github.com/kuujo/onos.git /src/onos \
    && cd /src/onos \
    && curl -L -o bazel.sh https://github.com/bazelbuild/bazel/releases/download/0.15.2/bazel-0.15.2-installer-linux-x86_64.sh \
    && chmod +x bazel.sh \
    && ./bazel.sh --user \
    && export ONOS_ROOT=/src/onos \
    && ln -s /usr/lib/jvm/java-8-oracle/bin/jar /etc/alternatives/jar \
    && ln -s /etc/alternatives/jar /usr/bin/jar \
    && ~/bin/bazel build onos --verbose_failures --jobs 2 \
    && mkdir -p /src/tar \
    && cd /src/tar \
    && tar -xf /src/onos/bazel-bin/onos.tar.gz --strip-components=1 \
    && rm -rf /src/onos/bazel-* .git

# Second stage is the runtime environment
FROM anapsix/alpine-java:8_server-jre

# Change to /root directory
RUN apk update \
    && apk add curl \
    && mkdir -p /root/onos
WORKDIR /root/onos

# Install ONOS
COPY --from=builder /src/tar/ .

# Configure ONOS to log to stdout
RUN sed -ibak '/log4j.rootLogger=/s/$/, stdout/' $(ls -d apache-karaf-*)/etc/org.ops4j.pax.logging.cfg

COPY scripts /root/onos/bin/

# Ports
# 6653 - OpenFlow
# 6640 - OVSDB
# 8181 - GUI
# 8101 - ONOS CLI
# 9876 - ONOS intra-cluster communication
EXPOSE 6653 6640 8181 8101 9876
