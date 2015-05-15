FROM alpine:3.1

MAINTAINER Kevin Lefevre <klefevre@vsense.fr> (@archifleks)

ENV BRANCH develop

RUN apk --update add \
    git \
    tzdata \
    python \
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
    && git clone -b $BRANCH --single-branch https://github.com/SiCKRAGETV/SickRage /sickrage \
    && mkdir /config /downloads \
    && adduser -D -h /sickrage -s /sbin/nologin -u 5001 sickrage \
    && chown -R sickrage:sickrage /sickrage /config /downloads \
    && apk del deps \
    && rm -rf /var/cache/apk/*

VOLUME /config /downloads

EXPOSE 8081

CMD ["--datadir=/config", "--nolaunch"]

ENTRYPOINT ["/sickrage/SickBeard.py"]
