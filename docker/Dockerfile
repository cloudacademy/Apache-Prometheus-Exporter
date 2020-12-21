FROM httpd:2.4

RUN apt-get update -y --allow-unauthenticated
RUN apt-get -y --allow-unauthenticated install apache2
ADD server-status.conf /etc/apache2/sites-available/
RUN a2ensite server-status.conf
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]

