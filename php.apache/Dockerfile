FROM php:8.0-apache

ENV COMPOSER_ALLOW_SUPERUSER=1

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

RUN apt-get update \
    && apt-get install -y --no-install-recommends locales apt-utils git libicu-dev g++ libpng-dev libjpeg-dev libxml2-dev libzip-dev libonig-dev libxslt-dev nodejs npm yarn -y;

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    echo "fr_FR.UTF-8 UTF-8" >> /etc/locale.gen && \
    locale-gen

RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
    mv composer.phar /usr/local/bin/composer

# RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -
# RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | /etc/apt/sources.list.d/yarn.list
# RUN apt-get update && sudo apt-get install -y yarn


# PHP Extensions
RUN docker-php-ext-install -j$(nproc) opcache pdo_mysql
COPY php.ini /usr/local/etc/php/php.ini




# Apache
COPY errors /errors
COPY conf/vhost.conf /etc/apache2/sites-available/000-default.conf
COPY conf/apache.conf /etc/apache2/conf-available/z-app.conf
#COPY index.php /app/index.php



RUN docker-php-ext-configure intl
RUN docker-php-ext-configure gd --with-jpeg=/usr/include/
RUN docker-php-ext-install pdo pdo_mysql opcache intl zip calendar dom mbstring zip gd xsl exif fileinfo
RUN pecl install apcu && docker-php-ext-enable apcu

WORKDIR /var/www/

RUN a2enmod rewrite remoteip && \
    a2enconf z-app