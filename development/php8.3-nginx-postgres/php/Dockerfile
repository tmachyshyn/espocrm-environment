FROM php:8.3-fpm

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
        libpq-dev \
        libc-client-dev \
        libkrb5-dev \
        libldap2-dev \
        libzmq3-dev \
        zlib1g-dev \
        freetype-dev \
        wget \
        git \
        unzip \
    ; \
    \
# Install php-zmq
    cd /home; \
    wget https://github.com/zeromq/libzmq/releases/download/v4.3.4/zeromq-4.3.4.tar.gz; \
    tar -xvzf zeromq-4.3.4.tar.gz; \
    wget https://github.com/zeromq/libzmq/archive/refs/heads/master.zip; \
    unzip master.zip; \
    cd /home/zeromq-4.3.4; \
    rm -r tests; \
    cd /home/libzmq-master; \
    cp -r tests /home/zeromq-4.3.4/tests; \
    cd /home/zeromq-4.3.4; \
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
    docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql; \
    docker-php-ext-install pdo pdo_pgsql pgsql; \
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
        | xargs -r realpath | xargs -r dpkg-query --search \
        | cut -d: -f1 \
        | sort -u \
        | xargs -rt apt-mark manual; \
    \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
    rm -rf /var/lib/apt/lists/*

# Set timezone
RUN echo "UTC" > /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata

# Install git, wget, cron
RUN apt-get update; \
    apt-get install -y git; \
    apt-get install -y wget; \
    apt-get install -y cron

# Install composer globally
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');" \
    && mkdir -p "/var/www/.composer" \
    && chown www-data:www-data "/var/www/.composer"

# Install npm
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash - \
    && apt-get install -y nodejs \
    npm

# Install Grunt
RUN npm update -g npm \
    && npm install -g grunt-cli

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

VOLUME /var/www/html

CMD ["php-fpm"]
