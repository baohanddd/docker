FROM ubuntu:20.04

ARG DEBIAN_FRONTEND=noninteractive

RUN printf "deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse\ndeb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse\ndeb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse\ndeb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse\ndeb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse\ndeb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse\ndeb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse\ndeb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse\ndeb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse\ndeb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse" > /etc/apt/sources.list
RUN apt-get update
RUN apt-get install software-properties-common -y
RUN apt-get install curl vim unzip git wget -y

RUN apt-get install php7.4-fpm php7.4-cli php7.4-common php7.4-dev php7.4-json php7.4-opcache php7.4-mbstring php7.4-curl php-redis php-msgpack php-yac php-ds php-pear php7.4-zip php7.4-gd php7.4-xml php7.4-bcmath -y

#RUN curl -sS https://getcomposer.org/installer -o composer-setup.php
#RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer
COPY php7.2/composer /usr/local/bin/composer
RUN chmod +x /usr/local/bin/composer
RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
RUN composer self-update

RUN pecl channel-update pecl.php.net
RUN mkdir -p /tmp/pear/cache

RUN pecl install psr
RUN echo "extension=psr.so" > /etc/php/7.4/mods-available/psr.ini
RUN cd /etc/php/7.4/fpm/conf.d && ln -s /etc/php/7.4/mods-available/psr.ini 20-psr.ini
RUN cd /etc/php/7.4/cli/conf.d && ln -s /etc/php/7.4/mods-available/psr.ini 20-psr.ini

RUN pecl install phalcon
RUN echo "extension=phalcon.so" > /etc/php/7.4/mods-available/phalcon.ini
RUN cd /etc/php/7.4/fpm/conf.d && ln -s /etc/php/7.4/mods-available/phalcon.ini 20-phalcon.ini
RUN cd /etc/php/7.4/cli/conf.d && ln -s /etc/php/7.4/mods-available/phalcon.ini 20-phalcon.ini

RUN pecl install swoole
RUN echo "extension=swoole.so" > /etc/php/7.4/mods-available/swoole.ini
RUN cd /etc/php/7.4/fpm/conf.d && ln -s /etc/php/7.4/mods-available/swoole.ini 20-swoole.ini
RUN cd /etc/php/7.4/cli/conf.d && ln -s /etc/php/7.4/mods-available/swoole.ini 20-swoole.ini

RUN pecl install mongodb
RUN echo "extension=mongodb.so" > /etc/php/7.4/mods-available/mongodb.ini
RUN cd /etc/php/7.4/fpm/conf.d && ln -s /etc/php/7.4/mods-available/mongodb.ini 20-mongodb.ini
RUN cd /etc/php/7.4/cli/conf.d && ln -s /etc/php/7.4/mods-available/mongodb.ini 20-mongodb.ini

RUN sed -i "s/;daemonize = yes/daemonize = no/g" /etc/php/7.4/fpm/php-fpm.conf
RUN sed -i "s/listen = \/run\/php\/php7.4-fpm.sock/listen = 9000/g" /etc/php/7.4/fpm/pool.d/www.conf
RUN sed -i "s/; max_input_vars = 1000/max_input_vars = 10000/g" /etc/php/7.4/fpm/php.ini
RUN sed -i "s/display_errors = Off/display_errors = On/g" /etc/php/7.4/fpm/php.ini

RUN mkdir -p /run/php && chown -R www-data:www-data /run/php

# install gearman extension
RUN LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/pkg-gearman -y
RUN apt-get update
RUN apt-get install php-gearman -y
RUN apt-get install php-bcmath -y

# for php-cs-fixer
RUN composer global require 'squizlabs/php_codesniffer=*'
RUN ln -s /root/.composer/vendor/bin/phpcs /usr/local/bin/phpcs
RUN composer global require friendsofphp/php-cs-fixer
RUN ln -s /root/.composer/vendor/bin/php-cs-fixer /usr/local/bin/php-cs-fixer

# for i18n
RUN apt-get install -y locales
RUN locale-gen en_US.UTF-8

# for cleaning...
RUN apt-get clean autoclean \
        apt-get autoremove -y \
        rm -rf /var/lib/{apt,dpkg,cache,log}/

EXPOSE 9000
CMD /usr/sbin/php-fpm7.4
