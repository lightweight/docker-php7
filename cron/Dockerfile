FROM kiwilightweight/php-fpm53
MAINTAINER Dave Lane <dave@davelane.nz> (@lightweight) https://davelane.nz

# cron is already installed in the base image

# set up cron tasks
RUN set -ex \
    && { \
        echo '# cron jobs for Drupal - dave@davelane.nz'; \
        echo 'PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin'; \
        echo 'DRUPAL=/var/www/html'; \
        echo 'OUT=/var/log/cron.log'; \
        echo '# Run cron every 5 minutes'; \
        echo '*/5 * * * * root cd $DRUPAL && php /var/www/html/vendor/bin/drush.php cron >> $OUT'; \
        echo '# Just testing'; \
        echo '*/5 * * * * root echo "Running cron at $(date)" >> $OUT'; \
        echo '# empty line'; \
    } | tee /etc/cron.d/drupal-cron

RUN touch 0644 /var/log/cron.log

CMD cron && tail -f /var/log/cron.log
