FROM php:7.1-fpm

ARG PHP_TIMEZONE
ARG PHP_MEMORY_LIMIT

RUN apt-get update && apt-get install -y \
    openssl \
    git \
    unzip \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) \
        gd \
        pdo \
        pdo_mysql \
        opcache \
        zip

RUN apt-get install -y libicu-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl

RUN pecl install xdebug

RUN docker-php-ext-enable \
    xdebug

# Install Blackfire
RUN version=$(php -r "echo PHP_MAJOR_VERSION.PHP_MINOR_VERSION;") \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp \
    && mv /tmp/blackfire-*.so $(php -r "echo ini_get('extension_dir');")/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > $PHP_INI_DIR/conf.d/blackfire.ini    

# Set timezone
RUN ln -snf /usr/share/zoneinfo/${PHP_TIMEZONE} /etc/localtime && echo ${PHP_TIMEZONE} > /etc/timezone

# Install composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer

COPY composer/config.json /var/www/.composer/config.json

# Copy xdebug and php config.
COPY conf.d/* /usr/local/etc/php/conf.d/

USER www-data

WORKDIR /var/www/project
