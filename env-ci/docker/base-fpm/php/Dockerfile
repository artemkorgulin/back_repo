FROM php:8.0-fpm

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libpcre3-dev \
    libssl-dev \
    zip \
    libzip-dev \
    unzip \
    mc \
    cron

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql exif pcntl bcmath gd zip

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

COPY www.conf /usr/local/etc/php-fpm.d/www.conf
RUN rm /usr/local/etc/php-fpm.d/zz-docker.conf; \
    mkdir -p /socket; \
    chown www:www /socket

COPY crontab /etc/cron.d/crontab
RUN chmod 0600 /etc/cron.d/crontab
RUN crontab /etc/cron.d/crontab
RUN touch /var/log/cron.log
RUN chown  www:www  /var/log/cron.log

# Change current user to www
USER www

# Set working directory
WORKDIR /var/www

VOLUME ["/var/www", "/socket", "/var/log/php"]

CMD ["php-fpm", "--nodaemonize"]

