ARG phpversion

FROM php:$phpversion-fpm

RUN apt-get update && apt-get install -y libpq-dev \
   vim \
   curl \
   icu-devtools \
   libicu-dev \
   libzip-dev \
   libpng-dev \
   libfreetype6-dev \
   libjpeg62-turbo-dev \
   libmemcached-dev \
   zlib1g-dev

# Tools
RUN docker-php-ext-install intl \
    && docker-php-ext-install zip \
    && docker-php-ext-install bcmath

# Postgres
RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql

# Redis
RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis

# Xdebug
RUN pecl install xdebug \
	&& docker-php-ext-enable xdebug

# Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
