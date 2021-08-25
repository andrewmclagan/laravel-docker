ARG ARCH=
ARG PHP_VERSION=

FROM ${ARCH}php:${PHP_VERSION}-fpm-alpine3.13

ENV PHP_ENVIRONMENT=
ENV TERM='xterm-256color'
ENV TZ='Australia/Sydney'
ENV PATH="$PATH:/var/www/vendor/bin"
ENV COMPOSER_HOME /tmp
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_VERSION 2.0.12

#
#--------------------------------------------------------------------------
# Dependencies and extensions
#--------------------------------------------------------------------------
#

RUN apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
    && apk add --no-cache \
        bash \
        # Yes, weare aware that we install curl twice.
        curl \ 
        freetype \
        zip \
        unzip \
        libzip-dev \
        libpng \
        libjpeg-turbo \
        freetype-dev \
        libpng-dev \
        libjpeg-turbo-dev \  
        mariadb-client \ 
    # Redis extension
    && pecl install -o -f redis \
    && docker-php-ext-enable redis \        
    && rm -rf /tmp/pear \
    # lib zip
    && docker-php-ext-configure zip \
    # Misic extensions
    && docker-php-ext-install \
        zip \
        pdo \
        pdo_mysql \
        mysqli \
        opcache \
        pcntl \
        bcmath \
        fileinfo \
        exif \        
    # GD image extensions
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && docker-php-ext-install -j${NPROC} gd \
    # remove caches
    && apk del -f .build-deps  

#
#--------------------------------------------------------------------------
# Composer
#--------------------------------------------------------------------------
#

RUN set -eux; \
  curl --silent --fail --location --retry 3 --output /tmp/installer.php --url https://raw.githubusercontent.com/composer/getcomposer.org/cb19f2aa3aeaa2006c0cd69a7ef011eb31463067/web/installer; \
  php -r " \
    \$signature = '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5'; \
    \$hash = hash('sha384', file_get_contents('/tmp/installer.php')); \
    if (!hash_equals(\$signature, \$hash)) { \
      unlink('/tmp/installer.php'); \
      echo 'Integrity check failed, installer is either corrupt or worse.' . PHP_EOL; \
      exit(1); \
    }"; \
  php /tmp/installer.php --no-ansi --install-dir=/usr/bin --filename=composer --version=${COMPOSER_VERSION}; \
  composer --ansi --version --no-interaction; \
  rm -f /tmp/installer.php; \
  find /tmp -type d -exec chmod -v 1777 {} +    

#
#--------------------------------------------------------------------------
# Folders and permissions
#--------------------------------------------------------------------------
#

COPY ./optimise /var/optimise
COPY ./laravel.ini /usr/local/etc/php/conf.d/laravel.ini

RUN mkdir -p /var/www 
WORKDIR /var/www