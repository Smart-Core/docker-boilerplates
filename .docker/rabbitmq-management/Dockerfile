FROM rabbitmq:3.8.9-management-alpine

RUN rabbitmq-plugins enable --offline rabbitmq_federation_management

#rabbitmq_clustering rabbitmq_mqtt rabbitmq_stomp

RUN rabbitmq-plugins disable rabbitmq_prometheus

RUN apk add --no-cache \
    bash \
    bash-completion \
    mc \
    htop

RUN rm -rf /var/cache/apk/* && \
    rm -rf /tmp/*
