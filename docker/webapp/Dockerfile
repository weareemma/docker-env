ARG PHP_IMAGE=php:8.1-fpm-buster
FROM ${PHP_IMAGE}

RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
    curl debconf subversion git apt-transport-https apt-utils \
    build-essential locales acl mailutils wget zip unzip gnupg gnupg1 gnupg2 \
    ffmpeg \
    # xml
    libxml2-dev \
    # xsl
    libxslt1-dev \
    # curl
    libcurl4-openssl-dev \
    # gd
    libwebp-dev libjpeg62-turbo-dev libpng-dev libxpm-dev libfreetype6-dev zlib1g-dev \
    # zip
    libzip-dev \
    # mbstring
    libonig-dev \
    # postgresql
    libpq-dev

RUN docker-php-ext-configure gd --enable-gd --with-webp --with-jpeg --with-freetype
RUN docker-php-ext-install -j$(nproc) gd

RUN docker-php-ext-install -j$(nproc) bcmath
RUN docker-php-ext-install -j$(nproc) ctype
RUN docker-php-ext-install -j$(nproc) fileinfo
RUN docker-php-ext-install -j$(nproc) mbstring
RUN docker-php-ext-install -j$(nproc) pdo
RUN docker-php-ext-install -j$(nproc) pdo_mysql
RUN docker-php-ext-install -j$(nproc) pdo_pgsql
RUN docker-php-ext-install -j$(nproc) mysqli
RUN docker-php-ext-install -j$(nproc) curl
RUN docker-php-ext-install -j$(nproc) xsl
RUN docker-php-ext-install -j$(nproc) xml
RUN docker-php-ext-install -j$(nproc) intl
RUN docker-php-ext-install -j$(nproc) zip

RUN apt-get install -y libmemcached-dev zlib1g-dev && \
    pecl install memcached && \
    echo "extension=memcached.so" > /usr/local/etc/php/conf.d/memcached.ini

RUN apt-get install -y librdkafka-dev \
    && pecl install channel://pecl.php.net/rdkafka-beta \
    && rm -rf /tmp/pear \
    && echo "extension=rdkafka.so" > /usr/local/etc/php/conf.d/rdkafka.ini

COPY docker/webapp/php.ini /usr/local/etc/php/php.ini
COPY docker/webapp/php-fpm-pool.conf /etc/php/8.1/pool.d/www.conf

# Install Node.js
RUN apt-get --allow-releaseinfo-change update -q -y \
    && apt-get install -q -y --no-install-recommends gnupg \
    && curl -sL https://deb.nodesource.com/setup_16.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g yarn

RUN rm -rf /var/lib/apt/lists/*
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

WORKDIR /var/www

RUN groupadd -g 1000 dev && \
    useradd -g dev -u 1000 dev && \
    mkdir -p /home/dev/.composer && chown -R dev:dev /home/dev && \
    chown -R dev:dev html

# USER dev

COPY --from=composer /usr/bin/composer /usr/bin/composer
RUN composer self-update
ENV COMPOSER_MEMORY_LIMIT=-1

WORKDIR /var/www/webapp

CMD  php-fpm;

EXPOSE 9000
