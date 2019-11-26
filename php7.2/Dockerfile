FROM ubuntu:16.04

RUN echo "deb-src http://archive.ubuntu.com/ubuntu xenial main restricted #Added by software-properties \ndeb http://mirrors.aliyun.com/ubuntu/ xenial main restricted \ndeb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted multiverse universe #Added by software-properties \ndeb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted \ndeb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted multiverse universe #Added by software-properties \ndeb http://mirrors.aliyun.com/ubuntu/ xenial universe \ndeb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe \ndeb http://mirrors.aliyun.com/ubuntu/ xenial multiverse \ndeb http://mirrors.aliyun.com/ubuntu/ xenial-updates multiverse \ndeb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse \ndeb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse #Added by software-properties \ndeb http://archive.canonical.com/ubuntu xenial partner \ndeb-src http://archive.canonical.com/ubuntu xenial partner \ndeb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted \ndeb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted multiverse universe #Added by software-properties \ndeb http://mirrors.aliyun.com/ubuntu/ xenial-security universe \ndeb http://mirrors.aliyun.com/ubuntu/ xenial-security multiverse" > /etc/apt/sources.list
RUN apt-get update
RUN apt-get install software-properties-common -y
RUN apt-get install curl vim unzip -y

RUN echo "50.97.198.58  packagecloud.io" >> /etc/hosts

RUN LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php -y
RUN curl -s "https://packagecloud.io/install/repositories/phalcon/stable/script.deb.sh" | bash
RUN apt-get update

RUN apt-get install php-fpm php-cli php-common php-dev php-json php7.3-opcache php-mbstring php-curl php-phalcon php-redis php-msgpack php-yac php-ds php-pear -y

RUN pecl install swoole
RUN echo "extension=swoole.so" > /etc/php/7.3/mods-available/swoole.ini
RUN cd /etc/php/7.3/fpm/conf.d && ln -s /etc/php/7.3/mods-available/swoole.ini 20-swoole.ini
RUN cd /etc/php/7.3/cli/conf.d && ln -s /etc/php/7.3/mods-available/swoole.ini 20-swoole.ini

RUN pecl install mongodb
RUN echo "extension=mongodb.so" > /etc/php/7.3/mods-available/mongodb.ini
RUN cd /etc/php/7.3/fpm/conf.d && ln -s /etc/php/7.3/mods-available/mongodb.ini 20-mongodb.ini
RUN cd /etc/php/7.3/cli/conf.d && ln -s /etc/php/7.3/mods-available/mongodb.ini 20-mongodb.ini

RUN curl -sS https://getcomposer.org/installer -o composer-setup.php
RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer
RUN composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

RUN sed -i "s/;daemonize = yes/daemonize = no/g" /etc/php/7.3/fpm/php-fpm.conf 
RUN sed -i "s/listen = \/run\/php\/php7.3-fpm.sock/listen = 9000/g" /etc/php/7.3/fpm/pool.d/www.conf 
RUN sed -i "s/; max_input_vars = 1000/max_input_vars = 10000/g" /etc/php/7.3/fpm/php.ini

RUN mkdir -p /run/php && chown -R www-data:www-data /run/php

# install gearman extension
RUN LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/pkg-gearman -y
RUN apt-get update
RUN apt-get install php-gearman -y
# for install rabbitmq php lib
RUN apt-get install php-bcmath -y

RUN composer global require 'squizlabs/php_codesniffer=*'
RUN ln -s /root/.composer/vendor/bin/phpcs /usr/local/bin/phpcs
RUN composer global require friendsofphp/php-cs-fixer
RUN ln -s /root/.composer/vendor/bin/php-cs-fixer /usr/local/bin/php-cs-fixer

RUN apt-get install -y locales
RUN locale-gen en_US.UTF-8

EXPOSE 9000
CMD /usr/sbin/php-fpm7.3
