FROM phpswoole/swoole:4.8-php8.0

RUN apt-get update && apt-get -y --no-install-recommends install \
        git \
        libpng-dev \
        libonig-dev \
        libxml2-dev \
        libpcre3-dev \
        libpq-dev \
        zip \
        libzip-dev \
        unzip \
        procps \
        mc \
        cron \
        nano
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pecl update-channels \
    && pecl install swoole \
    && docker-php-ext-enable swoole \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd pdo zip

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

COPY php.ini /usr/local/etc/php/php.ini

RUN mkdir -p /var/parser; \
    chown www:www /var/parser

COPY crontab /etc/cron.d/crontab
RUN chmod 0644 /etc/cron.d/crontab && touch /var/log/cron.log

# Change current user to www
USER www

# Set working directory
WORKDIR /var/www

VOLUME ["/var/www", "/var/parser", "/var/log/php"]

CMD ["php", "artisan", "octane:start", "--host=host.docker.internal", "--port=8082", "--workers=4", "--task-workers=4", "--max-requests=1000"]
