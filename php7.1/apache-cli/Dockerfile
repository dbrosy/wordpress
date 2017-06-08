FROM dbrosy/wordpress

# Add sudo in order to run wp-cli as the www-data user 
RUN apt-get update && apt-get install -y sudo less

# Add WP-CLI 
RUN curl -o /usr/local/bin/wpcli https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar

COPY wpcli.sh /usr/local/bin/wp
RUN chmod +x /usr/local/bin/wpcli /usr/local/bin/wp

# Add WP-CLI Autocomplete
RUN curl -o ~/wp-completion.bash https://raw.githubusercontent.com/wp-cli/wp-cli/master/utils/wp-completion.bash
RUN echo "source ~/wp-completion.bash" >> ~/.bashrc

# Cleanup
RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["apache2-foreground"]
