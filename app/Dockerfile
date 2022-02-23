FROM php:7.4.15-apache-buster
######## PHP image ###################################
# PHP7.4.15
# https://hub.docker.com/layers/php/library/php/7.4.15-apache-buster/images/sha256-9fb950d1ce5213d5937739d858a85c34a41351ac7d3ff9ca425fcd72cc80da11?context=explore
# PHP7.4
# https://hub.docker.com/layers/php/library/php/7.4-apache/images/sha256-60a55aa209eba9d23f85b4e360c4f9875c3a8aa0f28761a015b1ecf273de499d?context=explore
########################################################

ENV TZ=Asia/Tokyo
ENV SSLCONF=/etc/apache2/sites-available/default-ssl.conf

RUN echo "${TZ}" > /etc/timezone \
    && dpkg-reconfigure -f noninteractive tzdata

# setting enviroment files
ENV PHP_INI_DIR /usr/local/etc/php
COPY php.ini "$PHP_INI_DIR/"
COPY ssmtp.conf "/etc/ssmtp/"
COPY my.cnf "/etc/mysql/conf.d/"

# Install PHP extensions
RUN apt-get update \
    && apt-get install -y \
    zip \
    unzip \
    vim \
    libpng-dev \
    libpq-dev \
    libicu-dev libxml2-dev \
    libzip-dev \
    && docker-php-ext-install pdo_mysql \
    zip
RUN docker-php-ext-configure intl
RUN docker-php-ext-install intl

# Install APCu and APC backward compatibility
# https://github.com/merorafael/docker-php-apache/blob/master/7.0/Dockerfile
# About APCu or OPcache
# https://taitan916.info/blog/archives/3424
RUN pecl install apcu \
    && pecl install apcu_bc-1.0.3 \
    && docker-php-ext-enable apcu --ini-name 10-docker-php-ext-apcu.ini \
    && docker-php-ext-enable apc --ini-name 20-docker-php-ext-apc.ini

# apt のパッケージキャッシュをクリア Apache2でmod_rewriteを有効
RUN apt-get clean \
    && a2enmod rewrite 

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN apt-get install -y sendmail

# SSL化
COPY ./ssl/server.crt /etc/ssl/certs/
COPY ./ssl/server.key /etc/ssl/private/
RUN sed -i 's!/etc/ssl/certs/ssl-cert-snakeoil.pem!/etc/ssl/certs/server.crt!g' ${SSLCONF} \
    && sed -i 's!/etc/ssl/private/ssl-cert-snakeoil.key!/etc/ssl/private/server.key!g' ${SSLCONF}
# Apacheのmod_sslの有効化、default-ssl.confの有効化（sites-enabledからのシンボリックリンクを貼る）
RUN a2enmod ssl \
    && a2ensite default-ssl.conf

