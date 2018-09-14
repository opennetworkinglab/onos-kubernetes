FROM java:8

ENV ATOMIX_USER=atomix \
  ATOMIX_DATA_DIR=/var/lib/atomix/data \
  ATOMIX_LOG_DIR=/var/log/atomix

ARG ATOMIX_VERSION=3.0.5

RUN apt-get update && apt-get install -y iptables dnsutils

RUN set -x \
  && wget -q "https://oss.sonatype.org/content/repositories/releases/io/atomix/atomix-dist/$ATOMIX_VERSION/atomix-dist-$ATOMIX_VERSION.tar.gz" \
  && mkdir /opt/atomix \
  && tar -xzf "atomix-dist-$ATOMIX_VERSION.tar.gz" -C /opt/atomix \
  && rm -r "atomix-dist-$ATOMIX_VERSION.tar.gz"

COPY scripts /opt/atomix/bin/

RUN set -x \
  && useradd $ATOMIX_USER \
  && [ `id -u $ATOMIX_USER` -eq 1000 ] \
  && [ `id -g $ATOMIX_USER` -eq 1000 ] \
  && mkdir -p $ATOMIX_DATA_DIR $ATOMIX_LOG_DIR \
  && chown -R "$ATOMIX_USER:$ATOMIX_USER" /opt/atomix $ATOMIX_DATA_DIR $ATOMIX_LOG_DIR \
  && ln -s /opt/atomix/bin/* /usr/bin
