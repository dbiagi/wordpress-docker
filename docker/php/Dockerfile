FROM php:7.1-fpm

COPY ./conf /usr/local/etc/php/conf.d

# Install services
RUN apt-get update \
    && apt-get install -y \
    vim \
    apt-utils \
    curl \
    git \
    zip \
    libicu-dev \
    zlib1g-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng12-dev \
    g++

# Install composer
RUN curl -o /tmp/composer-setup.php https://getcomposer.org/installer \
    && curl -o /tmp/composer-setup.sig https://composer.github.io/installer.sig \
    && php -r "if (hash('SHA384', file_get_contents('/tmp/composer-setup.php')) !== trim(file_get_contents('/tmp/composer-setup.sig'))) { unlink('/tmp/composer-setup.php'); echo 'Invalid installer' . PHP_EOL; exit(1); }" \
    && php /tmp/composer-setup.php --no-ansi --install-dir=/usr/local/bin --filename=composer --snapshot \
    && rm -f /tmp/composer-setup.*

RUN docker-php-ext-install intl \
    && docker-php-ext-install opcache \
    && docker-php-ext-install pdo_mysql \
    && docker-php-ext-install mysqli \
    && docker-php-ext-install exif

# Install GD
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd

# Install xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug

# Install wp-cli
RUN curl -o /usr/local/bin/wp -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x /usr/local/bin/wp


RUN pecl install apcu && docker-php-ext-enable apcu

RUN mkdir /var/www/.composer/cache -p

RUN usermod -u 1001 www-data \
    && chown www-data:www-data /var/www -R

USER www-data

# @TODO Create bash_aliases file
RUN echo 'alias s3="php bin/console"' >> ~/.bashrc

# allow debug console commands
RUN export PHP_IDE_CONFIG="serverName=docker"
