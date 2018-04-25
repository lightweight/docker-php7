# docker-php7
A set of Docker containers for hosting a complex PHP7+ web application, e.g. Drupal, Grav, Wordpress

A Docker recipe for a non-trivial PHP website, in PHP-FPM mode (with the latest PHP7.x). Provides the main PHP container, a cron container, and an Nginx container, which can be proxied on your host machine. Assumes your database (if used) is running on the Docker host. 
