FROM alpine:3.1

MAINTAINER Kevin Lefevre <klefevre@vsense.fr> (@archifleks)

ENV TAG master

RUN apk --update add \
    git \
    tzdata \
    python \
    supervisor \
    unrar \
    zip \
    libxslt \
    && apk --update add --virtual deps \
    python-dev \
    build-base \
    py-pip \
    libxml2-dev \
    libxslt-dev \
    && pip install \
    cheetah \
    lxml \
    && git clone -b $TAG --single-branch https://github.com/SiCKRAGETV/SickRage /sickrage \
    && mkdir /config /downloads \
    && adduser -D -h /sickrage -s /sbin/nologin -u 5001 sickrage \
    && chown -R sickrage:sickrage /sickrage /config /downloads \
    && apk del deps \
    && rm -rf /var/cache/apk/*

VOLUME /config /downloads

COPY supervisord-sickrage.ini /etc/supervisor.d/supervisord-sickrage.ini

EXPOSE 8081

CMD ["supervisord", "-c", "/etc/supervisord.conf", "-n"]
