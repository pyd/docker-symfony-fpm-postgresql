# https://github.com/docker-library/php/blob/e81ce1cff2a8dc4a261c25a9164b8aefe498e218/8.0/alpine3.14/fpm/Dockerfile
# WORKDIR is /var/www/html
FROM php:8.0-fpm-alpine3.14

RUN apk update && apk add --no-cache \
  && apk add bash mc git icu-dev \
  && docker-php-ext-configure intl \
  && docker-php-ext-install intl \
  && docker-php-ext-enable intl \
  && docker-php-ext-install opcache

RUN apk add --no-cache --virtual .build-deps $PHPIZE_DEPS \
  && pecl install xdebug-3.1.0 \
  && docker-php-ext-enable xdebug \
  && apk del -f .build-deps

# installing pdo_pgsql returns an error:
#   checking for pg_config... not found
#   configure: error: Cannot find libpq-fe.h. Please specify correct PostgreSQL installation path
# installing postgresql-dev fixes this
# https://github.com/docker-library/php/issues/221
# note: installing libpq-dev only does not work 
RUN apk add postgresql-dev \
  && docker-php-ext-install pgsql pdo pdo_pgsql

RUN ln -s /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini
COPY ./conf.d/app.ini /usr/local/etc/php/conf.d/app.ini
COPY ./conf.d/xdebug.ini /usr/local/etc/php/conf.d/zzz-xdebug.ini

COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

ARG GIT_USER_NAME
ARG GIT_USER_EMAIL
RUN git config --global user.name "${GIT_USER_NAME}"
RUN git config --global user.email "${GIT_USER_EMAIL}"