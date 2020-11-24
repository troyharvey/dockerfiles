FROM php:7.4-fpm-alpine

COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN apk add --no-cache \
    libzip-dev

RUN docker-php-ext-install zip && \
    docker-php-source delete

RUN composer global require knuckleswtf/scribe

WORKDIR /app

ENTRYPOINT []
CMD ["php", "artisan", "scribe:generate"]
