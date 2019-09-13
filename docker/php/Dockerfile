FROM php:7.3-apache

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        unzip \
        curl \
        git \
#--------------------------------------------------------------------------
# PHP extensions
#--------------------------------------------------------------------------
        autoconf \
        dpkg-dev \
        pkg-config \
        re2c \
        file \
        gcc \
        g++ \
        make \
        libc-dev \
        musl-dev \
        libpq-dev \
        libmemcached-dev \
    ; \
    rm -rf /var/lib/apt/lists/* ; \
    pecl channel-update pecl.php.net ; \
#####################################
# MBString
# PDO
# OPcache
#####################################
    docker-php-ext-install \
        mbstring \
        pdo \
        pdo_pgsql \
        opcache \
    ; \
#####################################
# APCu
#####################################
    pecl install -o -f apcu && docker-php-ext-enable apcu ; \
#####################################
# PHP Memcached
#####################################
    pecl install -o -f memcached && docker-php-ext-enable memcached ; \
#####################################
# PHP Xdebug
#####################################
    pecl install -o -f xdebug && docker-php-ext-enable xdebug ; \
#####################################
# PHP Composer
#####################################
    curl -sS https://getcomposer.org/installer \
    | php -- --install-dir=/usr/local/bin --filename=composer ;

#--------------------------------------------------------------------------
# Environment
#--------------------------------------------------------------------------
VOLUME /var/www/html

WORKDIR /var/www/html
