FROM php:7-fpm
# modified from config by MAINTAINER https://github.com/helderco/
MAINTAINER Dave Lane <dave@davelane.nz> (@lightweight) https://davelane.nz

# Install PHP extensions
RUN apt-get update && apt-get install -y apt-utils git less libbz2-dev libc-client-dev \
    libcurl4-gnutls-dev libicu-dev libkrb5-dev libpng-dev \
    libpspell-dev libssl-dev libxml2-dev mariadb-client telnet unzip zip
RUN apt-get install -y net-tools vim dnsutils
# install cron and msmtp for outgoing email
RUN apt-get install -y cron msmtp
RUN rm -rf /var/lib/apt/lists/*
RUN docker-php-ext-configure imap --with-imap --with-imap-ssl --with-kerberos
RUN docker-php-ext-install bz2 curl gd imap intl mbstring mysqli \
    opcache pdo pdo_mysql pspell soap xmlrpc zip

RUN set -ex && cd /usr/local/etc && { \
        echo '[global]'; \
        echo 'error_log = /proc/self/fd/2'; \
        echo; \
        echo '[www]'; \
        echo '; if we send this to /proc/self/fd/1, it never appears'; \
        echo 'access.log = /proc/self/fd/2'; \
        echo; \
        echo '; Ensure worker stdout and stderr are sent to the main error log.'; \
        echo 'catch_workers_output = yes'; \
    } | tee php-fpm.d/docker.conf;

RUN set -ex && cd /usr/local/etc && { \
        echo '[www]'; \
        echo 'user = www-data'; \
        echo 'group = www-data'; \
        echo '; this does not appear to be supported by php 5.3.x'; \
        echo ';clear_env = no'; \
        #echo 'security.limit_extensions = .php'; \
        echo 'pm = ondemand'; \
        echo 'pm.max_children = 20'; \
        echo 'pm.start_servers = 2'; \
        echo 'pm.min_spare_servers = 1'; \
        echo 'pm.max_spare_servers = 3'; \
    } | tee php-fpm.d/www.conf;

RUN set -ex && cd /usr/local/etc && { \
        echo '[global]'; \
        echo 'daemonize = no'; \
        echo; \
        echo '[www]'; \
        echo 'listen = 9000'; \
    } | tee php-fpm.d/zz-docker.conf;

# address app-specific config requirements
RUN set -ex \
    && { \
        echo 'log_errors = on'; \
        echo 'display_errors = off'; \
        echo 'always_populate_raw_post_data = -1'; \
        echo 'date.timezone = "Pacific/Auckland"'; \
        echo 'cgi.fix_pathinfo = 0'; \
        echo 'sendmail_path = /usr/bin/msmtp -t'; \
        echo 'upload_max_filesize = 100M'; \
        echo 'post_max_size = 150M'; \
        echo 'memory_limit = 250M'; \
        echo 'max_execution_time = 1800'; \
        echo '[opcache]'; \
        echo 'opcache.enable = 1'; \
        echo 'opcache.memory_consumption = 128'; \
        echo 'opcache.max_accelerated_files = 8000'; \
        echo 'opcache.revalidate_freq = 60'; \
        echo 'opcache.use_cwd = 1'; \
        echo 'opcache.validate_timestamps = 1'; \
        echo 'opcache.save_comments = 1'; \
        echo 'opcache.enable_file_override = 0'; \
    } | tee /usr/local/etc/php/conf.d/php.ini;


# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN apt update && apt install -y sudo

WORKDIR /var/www/html

EXPOSE 9000

# Copy init scripts and custom .htaccess
COPY docker-entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

CMD ["php-fpm"]
