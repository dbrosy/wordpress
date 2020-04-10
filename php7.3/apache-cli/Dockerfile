FROM wordpress:5.4.0 as Wordpress

# ---------------------------------------
# Custom Config
# ---------------------------------------

# custom php config
RUN { \
		echo 'file_uploads = On'; \
		echo 'memory_limit = 128M'; \
		echo 'upload_max_filesize = 64M'; \
		echo 'post_max_size = 64M'; \
		echo 'max_execution_time = 180'; \
		echo 'max_input_time = 180'; \
		echo 'max_input_vars = 3000'; \
	} > /usr/local/etc/php/conf.d/uploads.ini
# custom php config

# custom .htaccess config
RUN { \
		echo '# BEGIN WordPress'; \
		echo '<IfModule mod_rewrite.c>'; \
		echo 'RewriteEngine On'; \
		echo 'RewriteBase /'; \
		echo 'RewriteRule ^index\.php$ - [L]'; \
		echo 'RewriteCond %{REQUEST_FILENAME} !-d'; \
		echo 'RewriteRule . /index.php [L]'; \
		echo '</IfModule>'; \
		echo '# END WordPress'; \
		echo ''; \
		echo '<IfModule mod_substitute.c>'; \
		echo 'SubstituteMaxLineLength 20M'; \
		echo '</IfModule>'; \
	} > /var/www/html/.htaccess
# custom .htaccess config
 
# custom redis config
RUN pecl install redis && \
	docker-php-ext-enable redis
# custom redis config

# Install wp-cli
ENV WORDPRESS_CLI_VERSION 2.4.0
ENV WORDPRESS_CLI_SHA512 4049c7e45e14276a70a41c3b0864be7a6a8cfa8ea65ebac8b184a4f503a91baa1a0d29260d03248bc74aef70729824330fb6b396336172a624332e16f64e37ef

# Install wp-cli pre-requisites
RUN set -eux; \
	\
	apt-get update; \
	apt-get install -y --no-install-recommends \
		sudo \
		less \
		mariadb-client \
	; \
	apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false; \
	rm -rf /var/lib/apt/lists/*

# Add WP-CLI
RUN curl -fsSL "https://github.com/wp-cli/wp-cli/releases/download/v${WORDPRESS_CLI_VERSION}/wp-cli-${WORDPRESS_CLI_VERSION}.phar" > /usr/local/bin/wp-cli.phar
COPY wp_su.sh /usr/local/bin/wp
RUN chmod +x /usr/local/bin/wp-cli.phar /usr/local/bin/wp

# Add WP-CLI Autocomplete
RUN curl -fsSL "https://raw.githubusercontent.com/wp-cli/wp-cli/v${WORDPRESS_CLI_VERSION}/utils/wp-completion.bash" > ~/wp-completion.bash
RUN echo "source ~/wp-completion.bash" >> ~/.bashrc

