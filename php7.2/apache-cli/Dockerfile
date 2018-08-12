FROM dbrosy/wordpress:php7.2

ENV WPCLI_VERSION=latest

# Add sudo in order to run wp-cli as the www-data user 
RUN apt-get update && apt-get install -y sudo less

# Add WP-CLI
RUN if [[ "$WPCLI_VERSION" == "latest" ]]; then \
        curl -o /usr/local/bin/wp-cli.phar https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar; \
        
    else \
        curl -L -o /usr/local/bin/wp-cli.phar "https://github.com/wp-cli/wp-cli/releases/download/v{$WPCLI_VERSION}/wp-cli-${WPCLI_VERSION}.phar"; \
    fi

COPY wp_su.sh /usr/local/bin/wp
RUN chmod +x /usr/local/bin/wp-cli.phar /usr/local/bin/wp

# Add WP-CLI Autocomplete
RUN if [[ "$WPCLI_VERSION" == "latest" ]]; then \
        curl -o ~/wp-completion.bash "https://raw.githubusercontent.com/wp-cli/wp-cli/master/utils/wp-completion.bash"; \
    else \
        curl -o ~/wp-completion.bash "https://raw.githubusercontent.com/wp-cli/wp-cli/v${WPCLI_VERSION}/utils/wp-completion.bash"; \
    fi

RUN echo "source ~/wp-completion.bash" >> ~/.bashrc

# Cleanup
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
