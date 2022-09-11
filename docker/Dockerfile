FROM --platform=$BUILDPLATFORM php:8.1.8-fpm-alpine as build-extensions

ARG APP_VER
ARG HOST_ARCH
ARG TARGETARCH

RUN apk add --no-cache bash
SHELL ["/bin/bash", "-c"]

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

RUN apk add --no-cache gmp gmp-dev icu icu-dev libzip-dev libzip libpq-dev \
    && if [[ "$TARGETARCH" = "arm64" ]]; then \
            docker-php-ext-configure opcache --build x86_64-pc-linux-musl --host aarch64-linux-musl --disable-opcache-jit \
            docker-php-ext-configure gmp --build x86_64-pc-linux-musl --host aarch64-linux-musl \
            docker-php-ext-configure intl --build x86_64-pc-linux-musl --host aarch64-linux-musl \
            docker-php-ext-configure zip --build x86_64-pc-linux-musl --host aarch64-linux-musl \
            docker-php-ext-configure pdo --build x86_64-pc-linux-musl --host aarch64-linux-musl \
            docker-php-ext-configure pdo_mysql --build x86_64-pc-linux-musl --host aarch64-linux-musl \
            docker-php-ext-configure pdo_pgsql --build x86_64-pc-linux-musl --host aarch64-linux-musl; \
        fi \
    && docker-php-ext-install -j"$(nproc)" opcache gmp intl zip pdo pdo_mysql pdo_pgsql \
	&& docker-php-ext-enable opcache gmp intl zip pdo pdo_mysql pdo_pgsql \
    && apk add --no-cache gmp icu libzip libpq

WORKDIR /app

USER app

EXPOSE 9000
CMD ["php-fpm"]

FROM php:8.1.8-fpm-alpine as build-pecl

RUN apk add --update --no-cache --virtual .build-dependencies $PHPIZE_DEPS \ 
    && pecl install apcu \
    && docker-php-ext-enable apcu \
    && pecl clear-cache \
    && apk del .build-dependencies