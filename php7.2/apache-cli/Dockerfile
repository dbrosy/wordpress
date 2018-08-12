FROM dbrosy/wordpress:php7.2

ENV WPCLI_VERSION=2.0.0

# Add sudo in order to run wp-cli as the www-data user 
RUN apt-get update && apt-get install -y sudo less

# Add WP-CLI 
RUN curl -o /usr/local/bin/wp-cli.phar https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar
# RUN curl -o /usr/local/bin/wp-cli.phar https://github.com/wp-cli/wp-cli/releases/download/v${WPCLI_VERSION}/wp-cli-${WPCLI_VERSION}.phar

COPY wp_su.sh /usr/local/bin/wp
RUN chmod +x /usr/local/bin/wp-cli.phar /usr/local/bin/wp

# Add WP-CLI Autocomplete
RUN curl -o ~/wp-completion.bash https://raw.githubusercontent.com/wp-cli/wp-cli/master/utils/wp-completion.bash
# RUN curl -o ~/wp-completion.bash https://raw.githubusercontent.com/wp-cli/wp-cli/v${WPCLI_VERSION}/utils/wp-completion.bash
RUN echo "source ~/wp-completion.bash" >> ~/.bashrc

# Cleanup
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
