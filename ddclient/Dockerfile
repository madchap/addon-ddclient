ARG BUILD_FROM
FROM $BUILD_FROM

# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
# Largely inspired from Dockerfile at https://github.com/linuxserver/docker-ddclient/blob/master/Dockerfile.aarch64
# Adapted for home-assistant add-on

# 3.10.0+ is a big rewrite.
# ARG DDCLIENT_VERSION=v3.9.0

RUN \
  echo "**** Install build packages ****" && \
  apk add --no-cache --virtual=build-dependencies \
    bzip2 \
    gcc \
    make \
    tar \
    wget && \
  echo "**** install runtime packages ****" && \
  apk add --no-cache \
    bind-tools \
    curl \
    inotify-tools \
    jq \
    perl \
    perl-digest-sha1 \
    perl-io-socket-inet6 \
    perl-io-socket-ssl \
    perl-json && \
  echo "***** install perl modules ****" && \
  curl -L http://cpanmin.us | perl - App::cpanminus && \
  cpanm \
    Data::Validate::IP \
    JSON::Any && \
  echo "**** install ddclient ****" && \
  if [ -z ${DDCLIENT_VERSION} ]; then \
    DDCLIENT_VERSION=$(curl -sX GET "https://api.github.com/repos/ddclient/ddclient/releases/latest" \
    | awk '/tag_name/{print $4;exit}' FS='[""]'); \
  fi && \
  mkdir -p \
    /tmp/ddclient && \
  curl -o \
  /tmp/ddclient.tar.gz -L \
    "https://github.com/ddclient/ddclient/archive/${DDCLIENT_VERSION}.tar.gz" && \
  tar xvf \
  /tmp/ddclient.tar.gz -C \
    /tmp/ddclient --strip-components=1 && \
  # cp /tmp/ddclient/ddclient /usr/bin/ddclient && \
  # above for <3.10 and below for >=3.10.0
  cp /tmp/ddclient/ddclient.in /usr/bin/ddclient.in && \
  ln -s /usr/bin/ddclient.in /usr/bin/ddclient && \
  mkdir -p /etc/ddclient/ && \
  cp /tmp/ddclient/sample-get-ip-from-fritzbox /etc/ddclient/get-ip-from-fritzbox && \
  echo "**** cleanup ****" && \
  apk del --purge \
    build-dependencies && \
  rm -rf \
    /config/.cpanm \
    /root/.cpanm \
    /tmp/*

# copy local files
COPY rootfs /

# ports and volumes
VOLUME /data
