FROM node:15.0.1-alpine

#RUN apt-get update && apt-get install -y \
#    curl \
#    wget \
#    ssh \
#    unzip \
#    zip

RUN rm -rf /var/cache/apk/* && \
    rm -rf /tmp/*

RUN apk update

RUN apk add --no-cache \
    autoconf \
    bash \
    binutils \
    bzip2-dev \
    curl \
    curl-dev

RUN rm -rf /var/cache/apk/* && \
    rm -rf /tmp/*
