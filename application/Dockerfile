FROM php:8.1.9RC1-apache-bullseye
WORKDIR /root

# Users
RUN addgroup --gid 1000 developer
RUN adduser --quiet --uid 1000 --gid 1000 --disabled-password --disabled-login --gecos "" developer

# Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
        php composer-setup.php --install-dir=/usr/local/bin/ && \
        cp /usr/local/bin/composer.phar /usr/local/bin/composer -v

# PHP Modules
RUN apt-get update
RUN apt-get install -y unzip zip libzip-dev && \
        docker-php-ext-install zip && \
        docker-php-ext-configure zip
        
RUN docker-php-ext-install pdo_mysql && \
        docker-php-ext-configure pdo_mysql


# Apache OpenID support
COPY ./apache/libapache2-mod-auth-openidc_2.4.11.3-1.bullseye+1_amd64.deb ./
RUN apt-get install libcjose0 libhiredis0.14
RUN dpkg -i libapache2-mod-auth-openidc_2.4.11.3-1.bullseye+1_amd64.deb
RUN a2enmod auth_openidc
COPY ./apache/herlios-auth.conf /etc/apache2/conf-available
RUN a2enconf herlios-auth

# Misc
RUN apt-get install -y vim

# SSL Config
COPY ./ssl/herlios-ca.crt /usr/local/share/ca-certificates
COPY ./ssl/herlios-front.crt /usr/local/share/ca-certificates

COPY ./ssl/herlios-ca.key /etc/ssl/private/
COPY ./ssl/herlios-front.key /etc/ssl/private/

RUN update-ca-certificates

# Apache config
RUN mkdir /var/www/herlios.test

COPY ./apache/herlios.conf /etc/apache2/sites-available/100-herlios.conf

RUN a2dissite 000-default
RUN a2ensite 100-herlios.conf
RUN a2enmod ssl

WORKDIR /var/www/
