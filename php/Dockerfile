FROM php:5.6-fpm

# Install Magento requirements
RUN \
    apt-get update -qq && \
    apt-get install -y -qq --no-install-recommends \
    wget \
    autoconf \
    git \
    jq \
    libicu-dev \
    libfreetype6-dev \
    libjpeg-dev \
    libmcrypt-dev \
    libpng-dev \
    libssl-dev \
    libxml2-dev \
    libxml2-utils \
    mailutils \
    ssh-client \
    ssmtp && \
    docker-php-ext-install -j$(nproc) \
    bcmath \
    gd \
    hash \
    intl \
    mcrypt \
    opcache \
    pdo_mysql \
    soap \
    zip && \
    yes "" | pecl install apcu-4.0.11 lzf mongo redis && \
    docker-php-ext-enable apcu lzf mongo redis && \
    rm -rf /var/lib/apt/lists/* && \
    perl -pi -e "s/mailhub=mail/mailhub=maildev/" /etc/ssmtp/ssmtp.conf && \
    perl -pi -e "s|;pm.status_path = /status|pm.status_path = /php_fpm_status|g" /usr/local/etc/php-fpm.d/www.conf

# Install Composer globally
RUN \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer

# Install grunt-cli globally
RUN \
    curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.5/install.sh | bash && \
    export NVM_DIR="$HOME/.nvm" && \
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" && \
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" && \
    nvm install node && \
    nvm use node && \
    npm install -g grunt-cli

# Install Phing through PEAR
RUN \
    pear channel-discover pear.phing.info && \
    pear install phing/phing

# Install custom PHP configuration
COPY conf/* /usr/local/etc/php/
COPY conf/xdebug.ini /usr/local/etc/php/conf.d/
ARG HOSTIP
RUN echo "xdebug.remote_host=$HOSTIP" >> /usr/local/etc/php/conf.d/xdebug.ini

# Install xdebug
# For PHP 5, the last xdebug supported version is 2.5.5
# See https://xdebug.org/download.php
# See http://pecl.php.net/package/Xdebug
RUN yes "" | pecl install xdebug-2.5.5

# Install N98 Mage Run
RUN curl -sL https://files.magerun.net/n98-magerun.phar -o n98-magerun.phar && chmod +x n98-magerun.phar && mv n98-magerun.phar /usr/local/bin/

