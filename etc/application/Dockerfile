# FROM php:8.2-fpm
FROM php:8.2-fpm-bullseye

ARG WWWGROUP
ARG XDEBUG_MODE="develop,trace"
ARG XDEBUG_VERSION="xdebug-3.2.2"
ARG XDEBUG_PORT="9003"
ARG NODEJS_VERSION="20.x"
ARG NPM_VERSION="10.1.0"

# Set working directory
WORKDIR /var/www

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=Europe/Moscow

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Base Install
RUN \
    apt-get update \
    && apt-get install -y \
        build-essential \
        libpng-dev \
        libjpeg62-turbo-dev \
        libfreetype6-dev \
        locales \
        zip \
        jpegoptim optipng pngquant gifsicle \
        vim \
        unzip \
        git \
        curl \
        libzip-dev

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install GD
# RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
# RUN docker-php-ext-install gd

# Install extensions
RUN docker-php-ext-install \
    pdo_mysql \
    mysqli \
    zip \
    bcmath \
    exif \
    pcntl \
    iconv

# RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
# RUN docker-php-ext-install gd

# Install redis
RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis

# Install Memcache
RUN pecl install memcache --enable-memcache-session=no \
    && docker-php-ext-enable memcache

# Install Memcached
# RUN pecl install igbinary \
#    && docker-php-ext-enable igbinary
# enable or RUN echo extension=igbinary.so >> /usr/local/etc/php/conf.d/igbinary.ini
# RUN pecl install msgpack \
#    && docker-php-ext-enable msgpack
# enable or RUN echo extension=msgpack.so >> /usr/local/etc/php/conf.d/msgpack.ini
# RUN pecl install --nobuild memcached \
#    && docker-php-ext-enable memcached
# enable or RUN echo extension=memcached.so >> /usr/local/etc/php/conf.d/memcached.ini

# Install Xdebug
RUN yes | pecl install ${XDEBUG_VERSION} \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.mode = ${XDEBUG_MODE}" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.client_host = host.docker.internal" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.client_port=${XDEBUG_PORT}" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && docker-php-ext-enable xdebug

# Install Composer
RUN \
    curl -sLS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

# Install NPM
RUN \
    curl -sL https://deb.nodesource.com/setup_${NODEJS_VERSION} | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@${NPM_VERSION}

# Install wkhtmltopdf
RUN \
    apt-get update \
    && apt-get -y install \
        libxrender1 \
        libfontconfig \
        xz-utils \
        fontconfig \
        libfontenc1 \
        x11-common \
        xfonts-75dpi \
        xfonts-base \
        xfonts-encodings \
        xfonts-utils \
        libfontconfig1-dev \
    && curl "https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6.1-2/wkhtmltox_0.12.6.1-2.bullseye_amd64.deb" -L -o "wkhtmltopdf.deb" \
    && dpkg -i wkhtmltopdf.deb \
    && export QT_QPA_PLATFORM=offscreen

# Clear cache
RUN \
    apt-get -y autoremove \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# RUN setcap "cap_net_bind_service=+ep" /usr/bin/php8.2

# # Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]
