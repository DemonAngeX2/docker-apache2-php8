FROM php:8-apache


ENV UID=1002
ENV GID=1002

RUN groupadd -g ${GID} webuser
RUN useradd webuser -u ${UID} -g ${GID} -m -s /bin/bash

RUN sed 's/www-data/webuser/' /etc/apache2/envvars

# SETUP APACHE2
RUN a2enmod rewrite
COPY ./php.ini "$PHP_INI_DIR/php.ini"
#RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
COPY ./apache/000-default.conf /etc/apache2/sites-available/000-default.conf

# PHP EXTENSION GD
RUN apt-get update && apt-get install -y \
    cron libzip-dev nano tree git zip unzip \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libicu-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install -j$(nproc) zip \
    && docker-php-ext-install -j$(nproc) opcache \
    && docker-php-ext-install -j$(nproc) intl
RUN docker-php-ext-install mysqli pdo pdo_mysql && docker-php-ext-enable pdo_mysql

COPY ./opcache.ini /usr/local/etc/php/conf.d/opcache.ini
RUN echo 'memory_limit = 512M' >> /usr/local/etc/php/conf.d/docker-php-memlimit.ini;

# COMPOSER
WORKDIR /app
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php composer-setup.php
RUN mv composer.phar /usr/local/bin/composer
RUN chmod a+x /usr/local/bin/composer
RUN rm composer-setup.php

WORKDIR /var/www/html

EXPOSE 80
