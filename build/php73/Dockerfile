FROM php:7.3-fpm

# ARGS
ARG CHANGE_SOURCE=false
ARG DEV_MODE=false

# Change Timezone
ARG TIME_ZONE=UTC
ENV TIME_ZONE ${TIME_ZONE}
RUN ln -snf /usr/share/zoneinfo/$TIME_ZONE /etc/localtime && echo $TIME_ZONE > /etc/timezone

# Change China Sources
COPY sources.list /etc/apt/china.sources.list
RUN if [ $CHANGE_SOURCE = true ]; then \
		mv /etc/apt/sources.list /etc/apt/source.list.bak && mv /etc/apt/china.sources.list /etc/apt/sources.list \
	;fi \
	; \
	apt-get -o Acquire::Check-Valid-Until=false update && apt-get -y upgrade \
	; \
# Install Base Components
	apt-get install -y --no-install-recommends mysql-client libc-client-dev libkrb5-dev libpq-dev libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng-dev libzip-dev ntpdate cron vim unzip git

####################################################################################
# Install Extension
####################################################################################

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/; \
	docker-php-ext-install -j$(nproc) sockets pdo pdo_mysql mysqli gd zip json fileinfo mbstring openssl opcache \
	; \
# Redis
	pecl install redis-4.2.0 \
	&& docker-php-ext-enable redis \
	; \
# Xdebug
	if [ $DEV_MODE = true ]; then \
		pecl install xdebug-2.7.0RC2 \
		&& docker-php-ext-enable xdebug \
	;fi
# PHP Composer
ADD composer.phar /usr/local/bin/composer
RUN chmod 755 /usr/local/bin/composer \
	; \
# Setup Composer Source
	if [ $CHANGE_SOURCE = true ]; then \
		composer config -g repo.packagist composer https://packagist.laravel-china.org \
	;fi
ENV LC_ALL C.UTF-8

ENTRYPOINT ["/bin/bash", "/home/start.sh"]
