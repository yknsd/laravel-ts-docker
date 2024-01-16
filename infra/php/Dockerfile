FROM php:8.1-fpm

COPY php.ini /usr/local/etc/php/

RUN apt-get update \
    && apt-get install -y \
    wget \
    git \
    unzip \
    libzip-dev

COPY --from=composer/composer /usr/bin/composer /usr/bin/composer

# Install composer
RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

# Install php extensions
RUN docker-php-ext-install pdo pdo_mysql

# Install Node.js 18
RUN apt-get install -y npm \
    && npm install n -g \
    && n 18 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /var/www

RUN chown -R www-data:www-data /var/www

RUN composer global require "laravel/installer"
