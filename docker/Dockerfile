FROM php:8.1.8-fpm-alpine

ARG APP_VER

RUN adduser -h /app -s /bin/bash -u 1000 -g 1000 -D app \
    && sed -i 's/^user \=.*/user = app/' /usr/local/etc/php-fpm.d/www.conf \
    && sed -i 's/^group \=.*/group = app/' /usr/local/etc/php-fpm.d/www.conf 

RUN apk add --no-cache wget unzip \
    && wget --progress=dot:giga https://github.com/FreshRSS/FreshRSS/archive/refs/tags/${APP_VER}.zip \
    && unzip -oq ${APP_VER}.zip \
    && mv FreshRSS-${APP_VER}/ /app \
    && apk del wget unzip \
    && mkdir -p /app/data /nginx \
    && chown -R app:app /app /nginx

RUN ulimit -n 1024000 \
    && apk add --no-cache gmp gmp-dev icu icu-dev libzip-dev libzip libpq-dev\
    && docker-php-ext-install -j"$(nproc)" opcache gmp intl zip pdo pdo_mysql pdo_pgsql \
	&& docker-php-ext-enable opcache gmp intl zip pdo pdo_mysql pdo_pgsql \
    && apk add --no-cache gmp icu libzip libpq

RUN ulimit -n 1024000 \
    && apk add --update --no-cache --virtual .build-dependencies $PHPIZE_DEPS \ 
    && pecl install apcu \
    && docker-php-ext-enable apcu \
    && pecl clear-cache \
    && apk del .build-dependencies

WORKDIR /app

USER app

EXPOSE 9000
CMD ["php-fpm"]
