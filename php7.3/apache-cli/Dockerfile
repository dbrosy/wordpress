FROM dbrosy/php:7.3-apache

# install the PHP extensions we need
RUN set -ex; \
	\
	savedAptMark="$(apt-mark showmanual)"; \
	\
	apt-get update; \
	apt-get install -y --no-install-recommends \
		libjpeg-dev \
		libpng-dev \
		libzip-dev \	
	; \
	\
	docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr; \
	docker-php-ext-install gd mysqli opcache zip; \
	\
# reset apt-mark's "manual" list so that "purge --auto-remove" will remove all build dependencies
	apt-mark auto '.*' > /dev/null; \
	apt-mark manual $savedAptMark; \
	ldd "$(php -r 'echo ini_get("extension_dir");')"/*.so \
		| awk '/=>/ { print $3 }' \
		| sort -u \
		| xargs -r dpkg-query -S \
		| cut -d: -f1 \
		| sort -u \
		| xargs -rt apt-mark manual; \
	\
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	rm -rf /var/lib/apt/lists/*

# set recommended PHP.ini settings
# see https://secure.php.net/manual/en/opcache.installation.php
RUN { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=2'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini
# https://codex.wordpress.org/Editing_wp-config.php#Configure_Error_Logging
RUN { \
		echo 'error_reporting = 4339'; \
		echo 'display_errors = Off'; \
		echo 'display_startup_errors = Off'; \
		echo 'log_errors = On'; \
		echo 'error_log = /dev/stderr'; \
		echo 'log_errors_max_len = 1024'; \
		echo 'ignore_repeated_errors = On'; \
		echo 'ignore_repeated_source = Off'; \
		echo 'html_errors = Off'; \
	} > /usr/local/etc/php/conf.d/error-logging.ini

RUN a2enmod rewrite expires

VOLUME /var/www/html

ENV WORDPRESS_VERSION 5.2
ENV WORDPRESS_SHA1 36459a4621b9e1909c606a98d08625b9e0e25bbc

RUN set -ex; \
	curl -o wordpress.tar.gz -fSL "https://wordpress.org/wordpress-${WORDPRESS_VERSION}.tar.gz"; \
	echo "$WORDPRESS_SHA1 *wordpress.tar.gz" | sha1sum -c -; \
# upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
	tar -xzf wordpress.tar.gz -C /usr/src/; \
	rm wordpress.tar.gz; \
	chown -R www-data:www-data /usr/src/wordpress

ENV WORDPRESS_CLI_GPG_KEY 63AF7AA15067C05616FDDD88A3A2E8F226F0BC06

ENV WORDPRESS_CLI_VERSION 2.2.0
ENV WORDPRESS_CLI_SHA512 2103f04a5014d629eaa42755815c9cec6bb489ed7b0ea6e77dedb309e8af098ab902b2f9c6369ae4b7cb8cc1f20fbb4dedcda83eb1d0c34b880fa6e8a3ae249d

# Install wp-cli
RUN set -ex; \
	\
	apt-get update; \
	apt-get install -y --no-install-recommends \
		gnupg \
		dirmngr \
		sudo \
		less \
		mysql-client \
	; \
	curl -o /usr/local/bin/wp-cli.gpg -fSL "https://github.com/wp-cli/wp-cli/releases/download/v${WORDPRESS_CLI_VERSION}/wp-cli-${WORDPRESS_CLI_VERSION}.phar.gpg"; \
	\
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --batch --keyserver ipv4.pool.sks-keyservers.net --recv-keys "$WORDPRESS_CLI_GPG_KEY"; \	
	gpg --batch --decrypt --output /usr/local/bin/wp-cli.phar /usr/local/bin/wp-cli.gpg; \
	command -v gpgconf && gpgconf --kill all || :; \
	rm -rf "$GNUPGHOME" /usr/local/bin/wp-cli.gpg; \
	\
	echo "$WORDPRESS_CLI_SHA512 */usr/local/bin/wp-cli.phar" | sha512sum -c -; \
	chmod +x /usr/local/bin/wp-cli.phar;

COPY wp_su.sh /usr/local/bin/wp;
RUN chmod +x /usr/local/bin/wp;

RUN set -ex; \
	\
	# Add WP-CLI Autocomplete
	curl -o ~/wp-completion.bash -fSL "https://raw.githubusercontent.com/wp-cli/wp-cli/v${WORDPRESS_CLI_VERSION}/utils/wp-completion.bash"; \
	echo "source ~/wp-completion.bash" >> ~/.bashrc;


COPY docker-entrypoint.sh /usr/local/bin/
# fix for file not having executable bit set
# RUN chmod +x exit/usr/local/bin/

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
