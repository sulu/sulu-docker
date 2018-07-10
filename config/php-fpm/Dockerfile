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
        opcache \
        zip

RUN apt-get install -y libicu-dev\
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
RUN php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer

COPY composer/config.json /var/www/.composer/config.json

# Copy xdebug and php config.
COPY conf.d/* /usr/local/etc/php/conf.d/

# TODO: Get the userid from host system (linux default user is 1000)
RUN usermod -u 1000 www-data
RUN groupmod -g 1000 www-data

# Create directories
RUN mkdir /var/www/.composer/cache
RUN mkdir /var/www/.ssh
RUN mkdir -p /var/www/project/var/logs

# Fix permissions and umask
RUN chown -R www-data /var/www
RUN chgrp -R www-data /var/www
RUN chmod -R g+rwx /var/www
RUN umask 0007

WORKDIR /var/www/project
