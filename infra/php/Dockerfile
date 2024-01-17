FROM php:8.1-fpm AS app

RUN apt-get update \
    && apt-get install -y \
    wget \
    git \
    unzip \
    libzip-dev

ENV WORK_USER myuser
ENV WORK_USER_GROUP mygroup
ENV WORK_DIR /app

# Create user
RUN useradd -m -s /bin/sh $WORK_USER && usermod -v 231072-296607 -w 231072-296607 $WORK_USER
RUN mkdir $WORK_DIR && chown myuser:myuser $WORK_DIR
WORKDIR $WORK_DIR
USER $WORK_USER

COPY ./infra/php/php.ini /usr/local/etc/php/
COPY --from=composer/composer /usr/bin/composer /usr/bin/composer

# Install composer
RUN curl -sS https://getcomposer.org/installer | php
USER root
RUN mv composer.phar /usr/local/bin/composer

# Install php extensions
RUN docker-php-ext-install pdo pdo_mysql

# Install Node.js 18
RUN apt-get install -y npm \
    && npm install n -g \
    && n 18 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV ENTRY_PATH /var/www/html
WORKDIR $ENTRY_PATH

COPY ./app $ENTRY_PATH

RUN chown -R $WORK_USER:$WORK_USER $ENTRY_PATH/storage \
    && chmod -R +w $ENTRY_PATH/storage \
    && chown -R $WORK_USER:$WORK_USER $ENTRY_PATH/bootstrap/cache \
    && chmod -R +w $ENTRY_PATH/bootstrap/cache

USER $WORK_USER
RUN composer install --no-dev
