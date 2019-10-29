FROM php:7.3-fpm

#COPY docker-php-source /usr/local/bin/
ENV PHPIZE_DEPS \
		autoconf \
		dpkg-dev \
		file \
		g++ \
		gcc \
		libc-dev \
		make \
		pkg-config \
		re2c

# persistent / runtime deps
RUN apt-get update && apt-get install -y \
		$PHPIZE_DEPS \
		ca-certificates \
		curl \
		xz-utils \
		libpng-dev \
	--no-install-recommends && rm -r /var/lib/apt/lists/*

RUN set -eux; \
	\
	savedAptMark="$(apt-mark showmanual)"; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		libcurl4-openssl-dev \
		libedit-dev \
		libsodium-dev \
		libsqlite3-dev \
		libssl-dev \
		libxml2-dev \
		zlib1g-dev \
		libzip-dev \
		${PHP_EXTRA_BUILD_DEPS:-} \
	; 

RUN docker-php-ext-install zip \
	gd \
#	fpm \
#	apcu \
#	ctype \
#	curl \
	pdo \
	pdo_mysql \
#	zlib \ 
	json \
	session
#	opcache


RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
   mv composer.phar /usr/local/bin/composer

#RUN docker-php-ext-enable sodium
#RUN docker-php-ext-enable pdo_mysql
RUN docker-php-ext-enable opcache
COPY php-fpm-pool.conf /usr/local/etc/php-fpm.d/www.conf

COPY php.ini /usr/local/etc/php/

RUN groupadd dev -g 999
RUN useradd dev -g dev -d /home/dev -m

# mise à l'heure
RUN unlink /etc/localtime
RUN ln -s /usr/share/zoneinfo/Europe/Paris /etc/localtime

RUN curl -sL https://deb.nodesource.com/setup_10.x | bash -
RUN apt-get install -y nodejs

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update && apt-get install -y yarn

WORKDIR /var/www/html

RUN yarn install

# Override stop signal to stop process gracefully
# https://github.com/php/php-src/blob/17baa87faddc2550def3ae7314236826bc1b1398/sapi/fpm/php-fpm.8.in#L163
STOPSIGNAL SIGQUIT

EXPOSE 9000
CMD ["php-fpm"]

