ARG PHP_VERSION=

FROM andrewmclagan/php:${PHP_VERSION}


COPY ./entrypoint /var/entrypoint

ENTRYPOINT ["/var/entrypoint"]

CMD php artisan horizon  
