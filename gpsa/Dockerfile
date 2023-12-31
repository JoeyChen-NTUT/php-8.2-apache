FROM php:8.1-apache

RUN export DEBIAN_FRONTEND=noninteractive

RUN curl -o /usr/local/share/ca-certificates/NTUT_CA.crt https://cnc.ntut.edu.tw/var/file/4/1004/img/1183/NTUT_Computer_And_Network_Center_Root_CA.crt
#https://raw.githubusercontent.com/JoeyChen-NTUT/php-8.0-apache/master/NTUT_Computer_And_Network_Center_Root_CA.crt
RUN update-ca-certificates --fresh
RUN apt-get clean
RUN apt-get update
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN apt-get install -y apt-utils cron

#modify php.ini for env require
RUN cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini
RUN sed -i -e 's/max_execution_time = 30/max_execution_time = 300/g' /usr/local/etc/php/php.ini && \
    sed -i -e 's/upload_max_filesize = 2M/upload_max_filesize = 256M/g' /usr/local/etc/php/php.ini && \
    sed -i -e 's/post_max_size = 8M/post_max_size = 256M/g' /usr/local/etc/php/php.ini && \
    sed -i -e 's/memory_limit = 128M/memory_limit = 1024M/g' /usr/local/etc/php/php.ini && \
    sed -i -e 's/;date.timezone =/date.timezone = Asia\/Taipei/g' /usr/local/etc/php/php.ini && \
    sed -i -e 's/max_file_uploads = 20/max_file_uploads = 300/g' /usr/local/etc/php/php.ini && \
    sed -i -e 's/max_input_time = 60/max_input_time = 120/g' /usr/local/etc/php/php.ini

#install zip extensions
RUN apt-get install -y \
        libzip-dev \
        zip \
  && docker-php-ext-configure zip \
  && docker-php-ext-install zip

#install gd extensions
RUN apt-get install -y \
    libfreetype6-dev \
        libmcrypt-dev \
        libjpeg-dev \
        libpng-dev \
        libonig-dev \
        libmcrypt-dev 
RUN docker-php-ext-configure gd \
#        --enable-gd-native-ttf \
        --with-freetype=/usr/include/freetype2 \
        --with-jpeg=/usr/include 
RUN docker-php-ext-install gd 
RUN docker-php-ext-enable gd
RUN docker-php-ext-install mbstring 
RUN docker-php-ext-enable mbstring
RUN docker-php-ext-install exif 
RUN docker-php-ext-enable exif
   
#install mysqli extensions
RUN docker-php-ext-install pdo pdo_mysql && docker-php-ext-enable pdo pdo_mysql

#enable mods
RUN a2enmod rewrite

COPY crontab /var/spool/cron/crontabs/root
RUN chmod 0644 /var/spool/cron/crontabs/root
RUN crontab /var/spool/cron/crontabs/root

COPY entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/entrypoint.sh
ENTRYPOINT ["entrypoint.sh"]

CMD ["apache2-foreground"]
