FROM php:8.0

ENV LANG C.UTF-8
ENV TZ=Asia/Tokyo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone


ARG BUILD_PACKAGES="build-base curl-dev git supervisor libpng libpng-dev libjpeg-turbo-dev libwebp-dev zlib-dev libxpm-dev imagemagick imagemagick-libs imagemagick-dev libzip-dev zip libxml2-dev"
ARG DEV_PACKAGES="bash"
RUN apk add --update \
    $BUILD_PACKAGES \
    $DEV_PACKAGES
RUN apk add --no-cache --repository http://dl-cdn.alpinelinux.org/alpine/edge/community/ --allow-untrusted gnu-libiconv
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php
RUN docker-php-ext-configure gd --enable-gd --with-jpeg
RUN docker-php-ext-install pdo pdo_mysql gd zip\
    && docker-php-ext-enable pdo_mysql gd zip
RUN curl -sS https://getcomposer.org/installer | php -- \
    --install-dir=/usr/local/bin \
    --filename=composer \
    --version=2.0.9

RUN apk --update --no-cache add autoconf g++ make && \
    pecl install -f xdebug imagick && \
    docker-php-ext-enable xdebug imagick && \
    apk del --purge autoconf g++ make

COPY ./docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh

COPY ./wait-for-it.sh /
RUN chmod +x /wait-for-it.sh

WORKDIR /app
