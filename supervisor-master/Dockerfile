FROM vsense/baseimage:alpine

MAINTAINER Kevin Lefevre <klefevre@vsense.fr> (@archifleks)

ENV BRANCH master

RUN apk --update add \
    git \
    tzdata \
    python \
    supervisor \
    unrar \
    zip \
    libxslt \
    py-pip
    && apk --update add --virtual deps \
    python-dev \
    build-base \
    libxml2-dev \
    libxslt-dev \
    && pip install \
    cheetah \
    lxml \
    && git clone -b $BRANCH --single-branch https://github.com/SiCKRAGETV/SickRage /sickrage \
    && mkdir /config /downloads \
    && adduser -D -h /sickrage -s /sbin/nologin -u 5001 sickrage \
    && chown -R sickrage:sickrage /sickrage /config /downloads \
    && apk del deps \
    && rm -rf /var/cache/apk/*

VOLUME /config /downloads

COPY supervisord-sickrage.ini /etc/supervisor.d/supervisord-sickrage.ini

EXPOSE 8081

CMD ["supervisord", "-c", "/etc/supervisord.conf", "-n"]
