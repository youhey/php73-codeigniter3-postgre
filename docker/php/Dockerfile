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
# Apache Documento root
#--------------------------------------------------------------------------
ENV APACHE_DOCUMENT_ROOT /work/public

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf

#--------------------------------------------------------------------------
# PHP conf
#--------------------------------------------------------------------------
COPY ./conf.d /usr/local/etc/php/conf.d/

# PHP Xdebug
ARG PHP_XDEBUG_INSTALL=1
# PHP Xdebug
ARG PHP_XDEBUG_DEFAULT_ENABLE=on
ARG PHP_XDEBUG_COVERAGE_ENABLE=on
ARG PHP_XDEBUG_REMOTE_ENABLE=on
ARG PHP_XDEBUG_REMOTE_AUTO_START=on
ARG PHP_XDEBUG_REMOTE_CONNECT_BACK=off
ARG PHP_XDEBUG_REMOTE_HOST=host.docker.internal
ARG PHP_XDEBUG_REMOTE_PORT=9000
ARG PHP_XDEBUG_IDEKEY=PHPSTORM
ARG PHP_XDEBUG_PROFILER_ENABLE=off
ARG PHP_XDEBUG_PROFILER_OUTPUT_DIR=/tmp

RUN set -ex; \
    { \
        echo "; Xdebug" ; \
        echo "xdebug.default_enable = ${PHP_XDEBUG_DEFAULT_ENABLE}" ; \
        echo "" ; \
        echo "; coverage" ; \
        echo "xdebug.coverage_enable = ${PHP_XDEBUG_COVERAGE_ENABLE}" ; \
        echo "" ; \
        echo "; remote debug" ; \
        echo "xdebug.remote_enable = ${PHP_XDEBUG_REMOTE_ENABLE}" ; \
        echo "xdebug.remote_autostart = ${PHP_XDEBUG_REMOTE_AUTO_START}" ; \
        echo "xdebug.remote_connect_back = ${PHP_XDEBUG_REMOTE_CONNECT_BACK}" ; \
        echo "xdebug.remote_host = ${PHP_XDEBUG_REMOTE_HOST}" ; \
        echo "xdebug.remote_port = ${PHP_XDEBUG_REMOTE_PORT}" ; \
        echo "xdebug.remote_handler = \"dbgp\"" ; \
        echo "xdebug.remote_mode = \"req\"" ; \
        echo "xdebug.remote_log = \"/proc/self/fd/2\"" ; \
        echo "xdebug.idekey = ${PHP_XDEBUG_IDEKEY}" ; \
        echo "" ; \
        echo "; profiler" ; \
        echo "xdebug.profiler_enable = ${PHP_XDEBUG_PROFILER_ENABLE}" ; \
        echo "xdebug.profiler_output_dir = ${PHP_XDEBUG_PROFILER_OUTPUT_DIR}" ; \
        echo "" ; \
    } >> /usr/local/etc/php/conf.d/z-xdebug.ini

#--------------------------------------------------------------------------
# Environment
#--------------------------------------------------------------------------
VOLUME /work
WORKDIR /work

env COMPOSER_PROCESS_TIMEOUT 3600
ENV COMPOSER_ALLOW_SUPERUSER 1
