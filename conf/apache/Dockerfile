FROM php:8.1-apache

RUN apt-get update && apt-get install -y

RUN apt install zip unzip -y

COPY ../../php.ini /usr/local/etc/php/

RUN curl -sS https://getcomposer.org/installer -o /tmp/composer-setup.php
RUN php /tmp/composer-setup.php --install-dir=/usr/local/bin --filename=composer

RUN mkdir /etc/certificate
RUN cd /etc/certificate && \
	openssl genrsa -des3 -passout pass:x -out server.pass.key 2048 && \
    openssl rsa -passin pass:x -in server.pass.key -out private.key && \
    rm server.pass.key && \
    openssl req -new -key private.key -out server.csr \
        -subj "/C=TR/ST=Istanbul/L=Istanbul/O=SSL/OU=SSL/CN=localhost" && \
    openssl x509 -req -days 365 -in server.csr -signkey private.key -out certificate.crt

ADD conf/apache/ssl-params.conf /etc/apache2/conf-available/ssl-params.conf
ADD conf/apache/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf

RUN a2enmod ssl
RUN a2enmod headers 
RUN a2enconf ssl-params && a2ensite default-ssl

RUN a2enmod rewrite && service apache2 restart