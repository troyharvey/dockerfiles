FROM php:7.4-fpm-alpine
LABEL maintainer="Troy Harvey"

# S6 /init process supervisor
ADD https://github.com/just-containers/s6-overlay/releases/download/v1.22.1.0/s6-overlay-amd64.tar.gz /tmp/
RUN tar xzf /tmp/s6-overlay-amd64.tar.gz -C /

# Dev dependencies (for compiling redis extension)
RUN apk add --no-cache --virtual .build-deps \
    $PHPIZE_DEPS

# Laravel dependencies
RUN apk add --no-cache \
    autoconf \
    freetype \
    freetype-dev \
    libjpeg-turbo \
    libjpeg-turbo-dev \
    libpng \
    libpng-dev \
    libzip-dev \
    oniguruma-dev \
    nginx

# php extensions
RUN pecl install redis
RUN docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-configure calendar && \
    docker-php-ext-install \
    bcmath \
    calendar \
    gd \
    mbstring \
    opcache \
    pdo_mysql \
    zip && \
    docker-php-ext-enable redis && \
    docker-php-source delete

# nginx logs
RUN ln -sf /dev/stdout /var/log/nginx/access.log && ln -sf /dev/stderr /var/log/nginx/error.log

# Cleanup dev dependencies
RUN apk del -f .build-deps

# Setup working directory
WORKDIR /var/www

ADD --chown=www-data:www-data ./conf/site.conf /etc/nginx/conf.d/default.conf
ADD --chown=www-data:www-data ./conf/php-fpm.conf /usr/local/etc/php-fpm.conf
ADD --chown=www-data:www-data ./conf/troyharvey.ini /usr/local/etc/php/conf.d/troyharvey.ini

ADD ./conf/nginx.conf /etc/nginx/nginx.conf
ADD ./conf/services.d /etc/services.d

EXPOSE 80
CMD "/init"
