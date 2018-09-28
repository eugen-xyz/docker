FROM php:7.1.10-apache

RUN apt-get update \
  && apt-get install -y openssh-server \
  && apt-get install -y openssh-client

RUN apt-get update \
    && apt-get install -y wget

RUN apt-get update \
    && apt-get install -y python2.7

# Install GD
RUN apt-get update \
    && apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install gd

# Install MCrypt
#RUN apt-get update \
#    && apt-get install -y libmcrypt-dev \
#    && docker-php-ext-install mcrypt

# Install Intl
RUN apt-get update \
    && apt-get install -y libicu-dev \
    && docker-php-ext-install intl

ENV XDEBUG_ENABLE 0
RUN pecl config-set preferred_state beta \
    && pecl install -o -f xdebug \
    && rm -rf /tmp/pear \
    && pecl config-set preferred_state stable

# Install Mysql
RUN docker-php-ext-install mysqli pdo_mysql

# Install mbstring
RUN docker-php-ext-install mbstring

# Install soap
RUN apt-get update \
    && apt-get install -y libxml2-dev \
    && docker-php-ext-install soap

# Install opcache
RUN docker-php-ext-install opcache

# Install PHP zip extension
RUN docker-php-ext-install zip

# Install xsl
RUN apt-get update \
    && apt-get install -y libxslt-dev \
    && docker-php-ext-install xsl

# Install ssmtp Mail Transfer Agent
RUN apt-get update \
    && apt-get install -y ssmtp \
    && apt-get clean \
    && echo "FromLineOverride=YES" >> /etc/ssmtp/ssmtp.conf \
    && echo 'sendmail_path = "/usr/sbin/ssmtp -t"' > /usr/local/etc/php/conf.d/mail.ini

# Install MySQL CLI Client
RUN apt-get update \
    && apt-get install -y mysql-client

# Install Node.js
RUN \
  cd /tmp && \
  wget http://nodejs.org/dist/v8.9.4/node-v8.9.4.tar.gz   && \
  tar xvzf node-v8.9.4.tar.gz  && \
  rm -f node-v8.9.4.tar.gz  && \
  cd node-v* && \
  ./configure && \
  CXX="g++ -Wno-unused-local-typedefs" make && \
  CXX="g++ -Wno-unused-local-typedefs" make install && \
  cd /tmp && \
  rm -rf /tmp/node-v* && \
  npm install -g npm && \
  echo '\n# Node.js\nexport PATH="node_modules/.bin:$PATH"' >> /root/.bashrc

# Install PHP Unit
ENV PHPUNIT_VERSION 7.0.1
RUN mkdir -p /root/src \
    && cd /root/src \
    && wget https://phar.phpunit.de/phpunit-${PHPUNIT_VERSION}.phar \
    && chmod +x phpunit-${PHPUNIT_VERSION}.phar \
    && mv phpunit-${PHPUNIT_VERSION}.phar /usr/local/bin/phpunit \
    && rm -rf /root/src \
    && phpunit --version

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer

# Define PHP_TIMEZONE env variable
ENV PHP_TIMEZONE Asia/Manila

# Configure Apache Document Root
ENV APACHE_DOC_ROOT /var/www/html

# Allow everyone to connect via SSH
RUN echo 'SSHD: ALL' >> /etc/hosts.allow

# Enable Apache mod_rewrite
RUN a2enmod rewrite
RUN a2enmod ssl
RUN usermod -u 1000 www-data
RUN groupmod -g 1000 www-data

RUN apt-get update \
    && apt-get install -y --no-install-recommends apt-utils

# Install pygments (for syntax highlighting)
RUN apt-get -qq update \
    && DEBIAN_FRONTEND=noninteractive apt-get -qq install -y --no-install-recommends python-pygments git ca-certificates asciidoc \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -d /home/army -ms /bin/bash -g root -G sudo -p army army
