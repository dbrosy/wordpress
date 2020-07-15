FROM wordpress:5-fpm-alpine as Wordpress

# ---------------------------------------
# Custom Config
# ---------------------------------------

# ---------------------------------------
# phpredis
# ---------------------------------------
RUN set -xe \
    && apk add --no-cache --update --virtual .phpize-deps $PHPIZE_DEPS \
    && pecl install -o -f redis \
    && docker-php-ext-enable redis \
    && apk del .phpize-deps


# ---------------------------------------
# WP-CLI
# ---------------------------------------
# install wp-cli dependencies
RUN apk add --no-cache \
# bash is needed for 'wp shell': https://github.com/wp-cli/shell-command/blob/b8dafcc2a2eba5732fdee70be077675a302848e9/src/WP_CLI/REPL.php#L104
		bash \
		less \
		mysql-client

# https://make.wordpress.org/cli/2018/05/31/gpg-signature-change/
# pub   rsa2048 2018-05-31 [SC]
#       63AF 7AA1 5067 C056 16FD  DD88 A3A2 E8F2 26F0 BC06
# uid           [ unknown] WP-CLI Releases <releases@wp-cli.org>
# sub   rsa2048 2018-05-31 [E]
ENV WORDPRESS_CLI_GPG_KEY 63AF7AA15067C05616FDDD88A3A2E8F226F0BC06

ENV WORDPRESS_CLI_VERSION 2.4.0
ENV WORDPRESS_CLI_SHA512 4049c7e45e14276a70a41c3b0864be7a6a8cfa8ea65ebac8b184a4f503a91baa1a0d29260d03248bc74aef70729824330fb6b396336172a624332e16f64e37ef

RUN set -ex; \
	\
	apk add --no-cache --virtual .fetch-deps \
		gnupg \
	; \
	\
	curl -o /usr/local/bin/wp.gpg -fSL "https://github.com/wp-cli/wp-cli/releases/download/v${WORDPRESS_CLI_VERSION}/wp-cli-${WORDPRESS_CLI_VERSION}.phar.gpg"; \
	\
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys "$WORDPRESS_CLI_GPG_KEY"; \
	gpg --batch --decrypt --output /usr/local/bin/wp /usr/local/bin/wp.gpg; \
	command -v gpgconf && gpgconf --kill all || :; \
	rm -rf "$GNUPGHOME" /usr/local/bin/wp.gpg; \
	\
	echo "$WORDPRESS_CLI_SHA512 */usr/local/bin/wp" | sha512sum -c -; \
	chmod +x /usr/local/bin/wp; \
	\
	apk del .fetch-deps; \
	\
	wp --allow-root --version



