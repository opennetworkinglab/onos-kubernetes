FROM ubuntu:16.04

USER root
WORKDIR /root

RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    iproute2 \
    iputils-ping \
    iptables \
    mininet \
    net-tools \
    tcpdump \
    vim \
    x11-xserver-utils \
    xterm

COPY scripts /root

EXPOSE 6633 6653 6640
