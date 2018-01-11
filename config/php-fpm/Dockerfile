# See https://github.com/docker-library/php/blob/master/7.1/fpm/Dockerfile
FROM php:7.1-fpm

ARG PHP_TIMEZONE
ARG PHP_MEMORY_LIMIT

RUN apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng12-dev

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) \
        gd \
        pdo \
        pdo_mysql \
        opcache

RUN pecl install xdebug

RUN docker-php-ext-enable \
    xdebug

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${PHP_TIMEZONE} /etc/localtime && echo ${PHP_TIMEZONE} > /etc/timezone

# Copy xdebug and php config.
COPY conf.d/* /usr/local/etc/php/conf.d/

WORKDIR /var/www/project
