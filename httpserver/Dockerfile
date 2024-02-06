FROM httpd:2.4
COPY ./my-httpd.conf /usr/local/apache2/conf/httpd-custom.conf
RUN echo "Include ./conf/httpd-custom.conf" >> /usr/local/apache2/conf/httpd.conf
