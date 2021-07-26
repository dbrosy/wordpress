FROM wordpress:5.8.0 as Wordpress

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
		echo 'default_socket_timeout = 300'; \
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
ENV WORDPRESS_CLI_VERSION 2.5.0
ENV WORDPRESS_CLI_SHA512 08dd9035fda1d529807380d5b757839e2809e289eb1a698fe33e7e21a1431d3f77c551c2b2db5adc55083d5075ea4137407994111890f765e790a97e6d9ca7af

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

# Fix cache permission issue adding .wp-cli directory
RUN mkdir /var/www/.wp-cli \
    && chown www-data:www-data /var/www/.wp-cli
