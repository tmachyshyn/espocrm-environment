FROM php:8.1-apache

# Install php libs
RUN set -ex; \
    \
    aptMarkList="$(apt-mark showmanual)"; \
    \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        libjpeg-dev \
        libpng-dev \
        libzip-dev \
        libxml2-dev \
        libc-client-dev \
        libkrb5-dev \
        libldap2-dev \
        libzmq3-dev \
        zlib1g-dev \
        freetype-dev \
        wget \
        git \
    ; \
    \
# Install php-zmq
    cd /home; \
    wget https://github.com/zeromq/libzmq/releases/download/v4.3.2/zeromq-4.3.2.tar.gz; \
    tar -xvzf zeromq-4.3.2.tar.gz; \
    cd /home/zeromq-4.3.2; \
    ./configure; \
    make; \
    make install; \
    ldconfig; \
    ldconfig -p | grep zmq; \
    cd /home; \
    git clone https://github.com/mkoppanen/php-zmq.git; \
    cd /home/php-zmq; \
    phpize && ./configure; \
    make; \
    make install; \
    \
# END Instalation php-zmq
    \
    docker-php-ext-install pdo_mysql; \
    docker-php-ext-install zip; \
    docker-php-ext-configure gd --with-jpeg --with-webp --with-freetype; \
    docker-php-ext-install gd; \
    PHP_OPENSSL=yes docker-php-ext-configure imap --with-kerberos --with-imap-ssl; \
    docker-php-ext-install imap; \
    docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/; \
    docker-php-ext-install ldap; \
    docker-php-ext-install exif; \
    docker-php-ext-enable zmq; \
    \
# reset a list of apt-mark
    apt-mark auto '.*' > /dev/null; \
    apt-mark manual $aptMarkList; \
    ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
        | awk '/=>/ { print $3 }' \
        | sort -u \
        | xargs -r dpkg-query -S \
        | cut -d: -f1 \
        | sort -u \
        | xargs -rt apt-mark manual; \
    \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
    rm -rf /var/lib/apt/lists/*

# Set timezone
RUN echo "UTC" > /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata

# Install required libs
RUN set -ex; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        git \
        wget \
        unzip \
        libldap-common \
    ; \
    rm -rf /var/lib/apt/lists/*

# Install composer globally
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');" \
    && mkdir -p "/var/www/.composer" \
    && chown www-data:www-data "/var/www/.composer"

# Install npm
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash - \
    && apt-get install -y nodejs

# Install Grunt
RUN npm update -g npm; \
    npm install -g grunt-cli; \
    mkdir -p /var/www/.npm; \
    mkdir -p /var/www/.config; \
    chown www-data:www-data /var/www/.npm; \
    chown www-data:www-data /var/www/.config

# Install PHPUNIT
RUN wget -P /tmp https://phar.phpunit.de/phpunit.phar \
    && mv /tmp/phpunit.phar /usr/local/bin/phpunit \
    && chmod +x /usr/local/bin/phpunit

# php.ini
RUN { \
    echo 'expose_php = Off'; \
    echo 'display_errors = On'; \
    echo 'display_startup_errors = On'; \
    echo 'log_errors = On'; \
    echo 'memory_limit=256M'; \
    echo 'max_execution_time=180'; \
    echo 'max_input_time=180'; \
    echo 'post_max_size=30M'; \
    echo 'upload_max_filesize=30M'; \
    echo 'date.timezone=UTC'; \
} > ${PHP_INI_DIR}/conf.d/espocrm.ini

RUN a2enmod rewrite

VOLUME /var/www/html

CMD ["apache2-foreground"]
