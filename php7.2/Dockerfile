FROM daocloud.io/library/ubuntu:xenial-20180808

RUN apt-get update
RUN apt-get install software-properties-common -y
RUN apt-get install curl vim unzip -y

RUN echo "50.97.198.58  packagecloud.io" >> /etc/hosts

RUN LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php -y
RUN curl -s "https://packagecloud.io/install/repositories/phalcon/stable/script.deb.sh" | bash
RUN apt-get update

RUN apt-get install php7.2-fpm php7.2-cli php7.2-common php7.2-dev php7.2-json php7.2-opcache php7.2-mbstring php7.2-curl php7.2-phalcon php-redis php-msgpack php-yac php-ds php-pear -y

RUN pecl install swoole
RUN echo "extension=swoole.so" > /etc/php/7.2/mods-available/swoole.ini
RUN cd /etc/php/7.2/fpm/conf.d && ln -s /etc/php/7.2/mods-available/swoole.ini 20-swoole.ini
RUN cd /etc/php/7.2/cli/conf.d && ln -s /etc/php/7.2/mods-available/swoole.ini 20-swoole.ini

RUN pecl install mongodb
RUN echo "extension=mongodb.so" > /etc/php/7.2/mods-available/mongodb.ini
RUN cd /etc/php/7.2/fpm/conf.d && ln -s /etc/php/7.2/mods-available/mongodb.ini 20-mongodb.ini
RUN cd /etc/php/7.2/cli/conf.d && ln -s /etc/php/7.2/mods-available/mongodb.ini 20-mongodb.ini

RUN cd ~ && curl -sS https://getcomposer.org/installer -o composer-setup.php
RUN php composer-setup.php --install-dir=/usr/local/bin --filename=composer

RUN sed -i "s/;daemonize = yes/daemonize = no/g" /etc/php/7.2/fpm/php-fpm.conf 
RUN sed -i "s/listen = \/run\/php\/php7.2-fpm.sock/listen = 9000/g" /etc/php/7.2/fpm/pool.d/www.conf 

RUN mkdir -p /run/php && chown -R www-data:www-data /run/php

EXPOSE 9000

WORKDIR /var/www/html
CMD /usr/sbin/php-fpm7.2