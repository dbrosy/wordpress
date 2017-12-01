FROM wordpress:php5.4

# install the PHP extensions we need 
RUN apt-get update \
    && apt-get install -y zlib1g-dev \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-install zip \
    && docker-php-ext-install exif

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
