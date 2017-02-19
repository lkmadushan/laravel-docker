FROM phusion/baseimage:latest

MAINTAINER Kalpa Perera

RUN DEBIAN_FRONTEND=noninteractive
RUN locale-gen en_US.UTF-8

ENV LANGUAGE=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV LC_CTYPE=UTF-8
ENV LANG=en_US.UTF-8
ENV TERM xterm

ENV HOME /root

# Enable ssh
RUN rm -f /etc/service/sshd/down
RUN /etc/my_init.d/00_regen_ssh_host_keys.sh

CMD ["/sbin/my_init"]

# Install Softwares
RUN apt-get update \
    && apt-get install -y \
       software-properties-common curl zip unzip git sqlite3

# Install Nginx and configurations
RUN apt-get update \
    && apt-get install -y nginx \
    && ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

COPY nginx.conf /etc/nginx/
COPY default /etc/nginx/sites-available/default

# Install PHP, Extensions, Composer and configurations
RUN add-apt-repository -y ppa:ondrej/php \
    && apt-get update \
    && apt-get install -y --force-yes php7.1-fpm php7.1-cli php7.1-gd \
       php7.1-pgsql php7.1-imap php7.1-mbstring php7.1-xml php7.1-curl \
       php7.1-sqlite3 php7.1-xdebug \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/local/bin/ --filename=composer \
    && mkdir /run/php

COPY php-fpm.conf /etc/php/7.1/fpm/php-fpm.conf
COPY xdebug.ini /etc/php/7.1/mods-available/xdebug.ini
COPY php.ini /etc/php/7.1/fpm/php.ini

# Set crontab
RUN (crontab -u root -l; echo "* * * * * php /var/www/html/artisan schedule:run >> /dev/null 2>&1" ) | crontab -u root -

# Set timezone
ARG TZ=UTC
ENV TZ ${TZ}
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime \
    && echo $TZ > /etc/timezone

# Set permissions
ARG PUID=33
ARG PGID=33
RUN groupmod -g $PGID www-data \
    && usermod -u $PUID www-data

RUN chown -R www-data:www-data /var/www
RUN chmod 755 /var/www

# Add startup scripts
COPY build/nginx /etc/service/nginx/run
RUN chmod +x /etc/service/nginx/run

COPY build/php-fpm /etc/service/php-fpm/run
RUN chmod +x /etc/service/php-fpm/run

COPY build/start-container /etc/my_init.d/start-container
RUN chmod +x /etc/my_init.d/start-container

EXPOSE 80

## Clean OS
RUN apt-get remove -y --purge software-properties-common \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
