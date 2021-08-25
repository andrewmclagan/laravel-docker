ARG PHP_VERSION=

FROM andrewmclagan/php:${PHP_VERSION}

#
#--------------------------------------------------------------------------
# Configuration
#--------------------------------------------------------------------------
#

RUN mkdir -p /var/log/cron && touch /var/log/crond.log

COPY ./crontab /etc/crontabs/root
COPY ./entrypoint /var/entrypoint

ENTRYPOINT ["/var/entrypoint"]

CMD crond -L /var/log/crond.log && tail -F /var/log/crond.log  
