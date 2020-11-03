FROM php:7.4.12-fpm-alpine

RUN rm -rf /var/cache/apk/* && \
    rm -rf /tmp/*

RUN apk update && apk add ca-certificates wget

RUN apk add --no-cache \
    autoconf \
    bash \
    bash-completion \
    binutils \
    bzip2-dev \
    curl \
    curl-dev \
    dpkg \
    dpkg-dev \
    g++ \
    gcc \
    git \
    gmp-dev \
    gnu-libiconv \
    grep \
    file \
    freetype \
    freetype-dev \
    icu-dev \
    jpeg \
    jpeg-dev \
    m4 \
    make \
    perl \
    libcurl \
    libjpeg-turbo \
    libjpeg-turbo-dev \
    libmemcached-dev \
    libmcrypt-dev \
    libpng \
    libpng-dev \
    libsodium-dev \
    libssh2 \
    libssh2-dev \
    libxpm \
    libxpm-dev \
    libxml2-dev \
    libzip-dev \
    postgresql-dev \
    postgresql-client \
    re2c \
    recode-dev \
    sqlite-dev \
    tzdata \
    unzip \
    zip

# GD For PHP 7.4
RUN docker-php-ext-configure gd --enable-gd --with-jpeg --with-freetype --with-xpm && docker-php-ext-install -j$(nproc) gd

RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql

### Base PHP Extensions
RUN docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) bz2 \
    && docker-php-ext-install -j$(nproc) calendar \
    && docker-php-ext-install -j$(nproc) exif \
    && docker-php-ext-install -j$(nproc) gmp \
    && docker-php-ext-install -j$(nproc) intl \
    && docker-php-ext-install -j$(nproc) opcache \
    && docker-php-ext-install -j$(nproc) pgsql \
    && docker-php-ext-install -j$(nproc) pcntl \
    && docker-php-ext-install -j$(nproc) pdo_pgsql \
    && docker-php-ext-install -j$(nproc) soap \
    && docker-php-ext-install -j$(nproc) sockets \
    && docker-php-ext-install -j$(nproc) zip

RUN pecl install apcu \
    && pecl install memcached \
    && pecl install redis \
    && pecl install timezonedb

RUN docker-php-ext-enable apcu \
    && docker-php-ext-enable memcached \
    && docker-php-ext-enable redis \
    && docker-php-ext-enable timezonedb

### Add Event extension
RUN apk add --no-cache libevent-dev openssl-dev
RUN pecl install event \
    &&  docker-php-ext-enable event \
    && mv /usr/local/etc/php/conf.d/docker-php-ext-event.ini /usr/local/etc/php/conf.d/docker-php-ext-zz-event.ini

### Add AMQP extension
RUN apk add --no-cache \
    --repository http://dl-cdn.alpinelinux.org/alpine/v3.12/main \
    rabbitmq-c \
    rabbitmq-c-dev \
    && pecl install amqp \
    && docker-php-ext-enable amqp

### MySQL
#RUN apk add --no-cache mysql-client \
#    && docker-php-ext-install -j$(nproc) mysqli pdo_mysql

### IMAP
RUN apk add --no-cache imap imap-dev c-client openssl-dev krb5 krb5-dev
RUN PHP_OPENSSL=yes docker-php-ext-configure imap --with-kerberos --with-imap --with-imap-ssl && docker-php-ext-install -j$(nproc) imap

### Install ssh2 extension
RUN wget -O libssh2.tar.gz https://www.libssh2.org/download/libssh2-1.9.0.tar.gz \
    && wget -O ssh2.tgz https://pecl.php.net/get/ssh2-1.2.tgz \
    && mkdir libssh2 && tar vxzf libssh2.tar.gz -C libssh2 --strip-components 1 \
    && mkdir ssh2 && tar vxzf ssh2.tgz -C ssh2 --strip-components 1 \
    && cd libssh2 && ./configure \
    && make && make install \
    && cd ../ssh2 && phpize && ./configure --with-ssh2 \
    && make && make install \
    && echo "extension=ssh2.so" >> /usr/local/etc/php/conf.d/ssh2.ini \
    && cd ../ && rm -rf libssh2.tar.gz ssh2.tgz ssh2 libssh2

# RUN pecl install ssh2

### imagemagick
#RUN apk add --no-cache imagemagick imagemagick-dev \
#    && pecl install imagick \
#    && docker-php-ext-enable imagick

# не работает :(
# RUN pecl install dbase && docker-php-ext-enable dbase

### xdebug
# RUN pecl install xdebug && docker-php-ext-enable xdebug

### Swoole
RUN apk add --no-cache procps \
    git \
    nano \
    openssl \
    wget \
    htop

RUN cd /tmp && git clone https://github.com/swoole/swoole-src.git && \
    cd swoole-src && \
    git checkout v4.5.5 && \
    phpize  && \
    ./configure  --enable-openssl && \
    make && make install

RUN touch /usr/local/etc/php/conf.d/swoole.ini && \
    echo 'extension=swoole.so' > /usr/local/etc/php/conf.d/swoole.ini

RUN wget -O /usr/local/bin/dumb-init https://github.com/Yelp/dumb-init/releases/download/v1.2.2/dumb-init_1.2.2_amd64
RUN chmod +x /usr/local/bin/dumb-init

### Custom soft
RUN apk add --no-cache sshpass fping

RUN apk add hping3 --update-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing

### Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/bin --filename=composer --quiet
ENV COMPOSER_ALLOW_SUPERUSER 1

RUN wget https://get.symfony.com/cli/installer -O - | bash && \
    mv /root/.symfony/bin/symfony /usr/local/bin/symfony && \
    symfony local:server:ca:install
