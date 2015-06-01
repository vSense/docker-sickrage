FROM alpine:3.2

MAINTAINER Kevin Lefevre <klefevre@vsense.fr> (@archifleks)

ENV PKGVER v4.0.23

RUN apk --update add \
    tzdata \
    supervisor \
    python \
    unrar \
    zip \
    libxslt \
    && apk --update add --virtual deps \
    tar \
    wget \
    python-dev \
    build-base \
    py-pip \
    libxml2-dev \
    libxslt-dev \
    && pip install \
    cheetah \
    lxml \
    && mkdir /config /downloads /sickrage \
    && wget --no-check-certificate https://github.com/SiCKRAGETV/SickRage/archive/"$PKGVER".tar.gz -O - | tar xz --strip-components 1 -C sickrage \
    && adduser -D -h /sickrage -s /sbin/nologin -u 5001 sickrage \
    && chown -R sickrage:sickrage /sickrage /config /downloads \
    && apk del deps \
    && rm -rf /var/cache/apk/*

VOLUME /config /downloads

COPY supervisord-sickrage.ini /etc/supervisor.d/supervisord-sickrage.ini

EXPOSE 8081

CMD ["supervisord", "-c", "/etc/supervisord.conf", "-n"]
