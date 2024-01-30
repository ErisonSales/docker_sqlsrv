FROM php:8.2-fpm
ARG uid=1000
ARG user=dev

# Instalação de pacotes e dependencias
RUN apt-get update && \
    apt-get -y --no-install-recommends install \
    git unzip libaio1 nano libldap2-dev unixodbc-dev npm imagemagick\
    && apt-get autoclean && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ && \
    docker-php-ext-install ldap && docker-php-ext-install pdo_mysql

RUN apt-get -y update \
    && apt-get install -y libicu-dev libpng-dev libzip-dev\ 
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-enable sodium \
    && docker-php-ext-install gd \
    && docker-php-ext-configure zip \
    && docker-php-ext-install zip 

RUN apt-get update && apt-get install -y \
    libmagickwand-dev --no-install-recommends \
    && pecl install imagick \
    && docker-php-ext-enable imagick

# Config de Usuario docker
RUN useradd -G www-data,root -u $uid -d /home/dev/ $user
RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user

# ####################################
# COMPOSER
# ####################################
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# ####################################
# SQLSRV
# ####################################
ENV ACCEPT_EULA=Y
RUN apt-get update && apt-get install -y gnupg2
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - 
RUN curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > /etc/apt/sources.list.d/mssql-release.list 
RUN apt-get update 
RUN ACCEPT_EULA=Y apt-get -y --no-install-recommends install msodbcsql17 unixodbc-dev 
RUN pecl install sqlsrv
RUN pecl install pdo_sqlsrv
RUN docker-php-ext-enable sqlsrv pdo_sqlsrv


USER $user
WORKDIR "/var/www"