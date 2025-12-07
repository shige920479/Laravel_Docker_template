FROM php:8.3-fpm

# ------------------------------------------------------------
# 1) 基本パッケージと PHP 拡張
# ------------------------------------------------------------
RUN apt-get update -qq && \
    apt-get install -y \
        git \
        unzip \
        zip \
        curl \
        vim \
        less \
        libzip-dev \
        libpng-dev \
        libjpeg-dev \
        libfreetype6-dev \
        libonig-dev \
        libicu-dev \
        libsqlite3-dev \
    && docker-php-ext-configure gd \
        --with-freetype=/usr/include/ \
        --with-jpeg=/usr/include/ \
    && docker-php-ext-install \
        pdo \
        pdo_mysql \
        pdo_sqlite \
        zip \
        gd \
        intl

# OPCache（Laravel で必須級の高速化）
RUN docker-php-ext-install opcache

# ------------------------------------------------------------
# 2) Composer
# ------------------------------------------------------------
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# ------------------------------------------------------------
# 3) 開発用ユーザー（developer）を作成（UID/GID=1000 に揃える）
# ------------------------------------------------------------
ARG UID=1000
ARG GID=1000

RUN groupadd -g ${GID} developer \
    && useradd -u ${UID} -g developer -m developer \
    && usermod -aG www-data developer

# ------------------------------------------------------------
# 4) 作業ディレクトリ
# ------------------------------------------------------------
WORKDIR /var/www/html

# ------------------------------------------------------------
# 5) PHP-FPM を developer ユーザーで動かす
# ------------------------------------------------------------
USER developer

# ------------------------------------------------------------
# 6) php.ini をコピー
# ------------------------------------------------------------
COPY php.ini /usr/local/etc/php/php.ini