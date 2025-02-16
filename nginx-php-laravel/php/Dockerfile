# docker build -t php:7.4-fpm-alpine-laravel .
# 命令行缩短 + no-cache 可以缩小点儿镜像大小
# docker build --no-cache -t php:7.4-fpm-alpine-laravel .

# FROM php:7.4-fpm-alpine
FROM php:7.4.25-fpm-alpine3.14

WORKDIR /www

ENV TZ=Asia/Shanghai

# 更新为国内镜像 并更新索引
RUN mv /etc/apk/repositories /etc/apk/repositories.bak
ADD ./repositories /etc/apk/
RUN apk update

RUN apk add --update \
        $PHPIZE_DEPS \
        freetype-dev \
        git \
        libjpeg-turbo-dev \
        libpng-dev \
        libxml2-dev \
        libzip-dev \
        openssh-client \
        php7-json \
        php7-openssl \
        php7-pdo \
        php7-pdo_mysql \
        php7-session \
        php7-simplexml \
        php7-tokenizer \
        php7-xml \
        imagemagick \
        imagemagick-libs \
        imagemagick-dev \
        php7-imagick \
        php7-pcntl \
        php7-zip \
        sqlite \
        tzdata \
    && docker-php-ext-install iconv soap sockets exif bcmath pdo_mysql pcntl \
    && docker-php-ext-configure gd --with-jpeg --with-freetype \
    && docker-php-ext-install gd \
    && docker-php-ext-install zip

# add mysqli
RUN printf "\n" | docker-php-ext-install mysqli

# add intl
RUN printf "\n" | apk add --update icu-dev \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl

# add mcrypt
RUN printf "\n" | apk add --update libmcrypt-dev \
    && pecl install mcrypt  \
    && docker-php-ext-enable mcrypt

# update curl # 一个curl漏洞的修复：https://snyk.io/vuln/SNYK-ALPINE314-CURL-1585251
RUN printf "\n" | apk add curl=7.79.1-r0

# add imagick
RUN printf "\n" | pecl install imagick \
    && docker-php-ext-enable --ini-name 20-imagick.ini imagick

# add pcov
RUN printf "\n" | pecl install pcov \
    && docker-php-ext-enable pcov

# add composer and edit composer repo.packagist
RUN printf "\n" | php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && mv composer.phar /usr/bin/composer \
    && composer self-update --clean-backups \
    && chmod +x /usr/bin/composer \
    && composer config -g repo.packagist composer https://packagist.phpcomposer.com

# change www-data's uid and gid for laravel folder permisstion
RUN printf "\n" | apk --no-cache add shadow \
    && rm -rf /var/cache/apk/* \
    && usermod -u 1000 www-data \
    && groupmod -g 1000 www-data

# image description
LABEL Author="Jack" Version="2021.10.15"