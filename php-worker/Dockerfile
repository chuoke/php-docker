
ARG PHP_VERSION
FROM php:${PHP_VERSION}-alpine


ARG APP_CODE_PATH_CONTAINER
ENV APP_CODE_PATH_CONTAINER=${APP_CODE_PATH_CONTAINER}


ARG ALPINE_APK_REPO
RUN if [ ${ALPINE_APK_REPO} ]; then \
        sed -i "s/dl-cdn.alpinelinux.org/${ALPINE_APK_REPO}/g" /etc/apk/repositories; \
    fi && \
    apk update \
    && apk upgrade \
    && apk add --no-cache openssl bash


# Add a non-root user:
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN addgroup -g ${PGID} phper && \
    adduser -D -G phper -u ${PUID} phper


RUN apk --update add wget \
  curl \
  bash \
  build-base \
  libmcrypt-dev \
  libxml2-dev \
  pcre-dev \
  zlib-dev \
  autoconf \
  cyrus-sasl-dev \
  libgsasl-dev \
  oniguruma-dev \
  openssl \
  openssl-dev \
  supervisor

# Install the pdo_mysql extension
RUN docker-php-ext-install pdo_mysql

# Enable the sockets extension
RUN docker-php-ext-enable sockets

# Install the intl extension
RUN apk add --no-cache icu-dev && \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl

# Install the pcntl extension
RUN docker-php-ext-install pcntl

# Install the gd extension
RUN apk add --no-cache \
        freetype-dev \
        libjpeg-turbo-dev \
        libpng-dev && \
    docker-php-ext-install -j$(nproc) gd

# Install the zip extension
RUN apk add --no-cache libzip-dev zip unzip && \
    docker-php-ext-install zip && \
    php -m | grep -q 'zip'

# Install the igbinary and redis extension
ARG EXT_IGBINARY_VERSION=3.1.5
ARG EXT_REDIS_VERSION=5.3.1

RUN docker-php-source extract \
    # igbinary
    && mkdir -p /usr/src/php/ext/igbinary \
    && curl -fsSL https://github.com/igbinary/igbinary/archive/$EXT_IGBINARY_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/igbinary --strip 1 \
    && docker-php-ext-install igbinary \
    # redis
    && mkdir -p /usr/src/php/ext/redis \
    && curl -fsSL https://github.com/phpredis/phpredis/archive/$EXT_REDIS_VERSION.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
    && docker-php-ext-configure redis --enable-redis-igbinary \
    && docker-php-ext-install redis \
    # cleanup
    && docker-php-source delete

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
    # mv composer.phar /usr/local/bin/composer && \
    php -r "unlink('composer-setup.php');"


ARG COMPOSER_REPO_PACKAGIST
ENV COMPOSER_REPO_PACKAGIST ${COMPOSER_REPO_PACKAGIST}

RUN if [ ${COMPOSER_REPO_PACKAGIST} ]; then \
    composer config -g repo.packagist composer ${COMPOSER_REPO_PACKAGIST} \
;fi


WORKDIR ${APP_CODE_PATH_CONTAINER}


COPY supervisord.conf /etc/supervisord.conf

ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisord.conf"]


RUN rm /var/cache/apk/* \
    && mkdir -p ${APP_CODE_PATH_CONTAINER}


WORKDIR /etc/supervisor/conf.d/
