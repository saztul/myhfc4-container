FROM hfcils/myhfc4-base:php5

LABEL author="Lutz Selke <ls@hfci.de>"

WORKDIR /var/www/
VOLUME "/etc/ssmtp"
ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["/usr/bin/supervisord", "--nodaemon", "--configuration", "/etc/supervisord.conf"]

RUN sed -i '/de_DE/s/^# //' /etc/locale.gen \
    && dpkg-reconfigure locales \
    && ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime \
    && dpkg-reconfigure -f noninteractive tzdata \
    && mkdir -p /var/www/files \
    && mkdir -p /var/www/logs/system \
    && touch /var/log/cron.log

# added libssl-dev=1.0.2g-1ubuntu4.10 to fix wkhtmltopdf ssl problems
# https://github.com/wkhtmltopdf/wkhtmltopdf/issues/3001
RUN wget https://downloads.wkhtmltopdf.org/0.12/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz && \
    tar xvf wkhtmltox-0.12.4_linux-generic-amd64.tar.xz && \
    mkdir -p /opt/bin && \
    mv wkhtmltox/bin/wkhtmlto* /opt/bin/ && \
    apt-get update && \
    apt-get install -y libssl-dev=1.0.2g-1ubuntu4.10 && \
    apt-mark hold libssl-dev

ADD ./php/* /etc/php/5.6/apache2/conf.d/
ADD ./vhosts/* /etc/apache2/sites-enabled/
ADD ./supervisor.conf /etc/supervisord.conf
ADD ./scripts/* /usr/local/bin/

RUN chmod +x /usr/local/bin/* \
    && chmod 0644 /etc/cron.d/* \
    && a2enmod vhost_alias

EXPOSE 80
