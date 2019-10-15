#FROM buildpack-deps:jessie
FROM httpd:2.4

#MAINTAINER Amin Mkh <mukh_amin@yahoo.com>
#
## installing required stuff
RUN apt-get update \
    && apt-get install -y --force-yes curl vim lbzip2 git  autoconf2.13 wget build-essential libxml2-dev \
    libapache2-mod-php5

#COPY conf/apache/httpd-2.4.41.tar.gz /opt/httpd-2.4.41.tar.gz
#
#
#RUN cd /opt \
#      && tar -xzf httpd-2.4.41.tar.gz \
#      && cd /opt/httpd-2.4.41 \
#      && ./configure --prefix=/usr/local/apache --enable-shared=max\
#      && make && make install \
#      && rm -rf /opt/httpd-2.4.41.tar.gz

#help modules
COPY conf/php/docker-php-* /usr/local/bin/

RUN cd /usr/local/bin && ls -la

#sertificates
COPY conf/keys/server.crt /usr/local/apache2/conf/server.crt
COPY conf/keys/server.key /usr/local/apache2/conf/server.key

#server configuration
COPY conf/apache/httpd.conf /usr/local/apache2/conf/httpd.conf

#vhost configuration
COPY conf/apache/vhosts/default.conf /usr/local/apache2/conf/extra/httpd-vhosts.conf
COPY conf/apache/vhosts/default-ssl.conf /usr/local/apache2/conf/extra/httpd-ssl.conf


## PHP extensions
#RUN cd /usr/local/bin \
#    docker-php-ext-configure pdo_mysql --with-pdo-mysql=mysqlnd \
#    && docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
#    && docker-php-ext-install pdo_mysql \
#    && docker-php-ext-install mbstring

#php5.3
ENV PHP_AUTOCONF=autoconf2.13

#RUN cd /opt \
#    && curl -SLO https://ftp.gnu.org/gnu/autoconf/autoconf-2.13.tar.gz \
#    && tar -xzf autoconf-2.13.tar.gz \
#    && ./configure --prefix=/usr --program-suffix=2.13   \
#    && make && make install \
#    && rm -rf /opt/autoconf-2.13.tar.gz

#RUN cd /opt \
#    && wget http://ftp.gnu.org/gnu/bison/bison-2.3.tar.gz \
#    && tar -xvzf bison-2.3.tar.gz \
#    && cd bison-2.3 \
#    && ./configure --prefix=/usr/local/bison --with-libiconv-prefix=/usr/local/libiconv/ \
#    && make && make install \
#    && rm -rf /opt/bison-2.3.tar.gz

#RUN cd /opt \
#    	&& curl --insecure -SLO http://launchpadlibrarian.net/140087283/libbison-dev_2.7.1.dfsg-1_amd64.deb \
#    	&& curl --insecure -SLO http://launchpadlibrarian.net/140087282/bison_2.7.1.dfsg-1_amd64.deb \
#    	&& dpkg -i libbison-dev_2.7.1.dfsg-1_amd64.deb \
#    	&& dpkg -i bison_2.7.1.dfsg-1_amd64.deb \
#    	&& rm *.deb


#COPY conf/php/compile_php_scripts_53 /opt/compile_php_scripts_53
#
#RUN chmod u+x /opt/compile_php_scripts_53
#
#RUN bash -c "/opt/compile_php_scripts_53"
ENV PHP_VERSION 5.3.29

ENV PHP_INI_DIR /usr/local/lib

RUN mkdir -p $PHP_INI_DIR/conf.d

# php 5.3 needs older autoconf
RUN set -x \
	&& apt-get update && apt-get install -y --force-yes autoconf2.13 \
	&& rm -r /var/lib/apt/lists/* \
	&& curl --insecure -SLO http://launchpadlibrarian.net/140087283/libbison-dev_2.7.1.dfsg-1_amd64.deb \
	&& curl --insecure -SLO http://launchpadlibrarian.net/140087282/bison_2.7.1.dfsg-1_amd64.deb \
	&& dpkg -i libbison-dev_2.7.1.dfsg-1_amd64.deb \
	&& dpkg -i bison_2.7.1.dfsg-1_amd64.deb \
	&& rm *.deb \
	&& curl --insecure -SL "http://php.net/get/php-$PHP_VERSION.tar.bz2/from/this/mirror" -o php.tar.bz2 \
	&& curl --insecure -SL "http://php.net/get/php-$PHP_VERSION.tar.bz2.asc/from/this/mirror" -o php.tar.bz2.asc \
#	&& gpg --verify php.tar.bz2.asc \
	&& mkdir -p /usr/src/php \
	&& tar -xf php.tar.bz2 -C /usr/src/php --strip-components=1 \
	&& rm php.tar.bz2* \
	&& cd /usr/src/php \
	&& ./buildconf --force \
	&& ./configure --disable-cgi \
		$(command -v apxs2 > /dev/null 2>&1 && echo '--with-apxs2' || true) \
    --with-config-file-path="$PHP_INI_DIR" \
    --with-config-file-scan-dir="$PHP_INI_DIR/conf.d" \
#		--with-mysql \
#		--with-mysqli \
#		--with-pdo-mysql \
		--with-mbstring \
		--with-soap \
#		--with-openssl-dir=/usr/local/ssl \
	&& make -j"$(nproc)" \
	&& make install \
	&& dpkg -r bison libbison-dev \
	&& apt-get purge -y --force-yes --auto-remove autoconf2.13 \
  && make clean


COPY conf/php/php.ini /usr/local/etc/php/php.ini

#RUN service apachectl restart

#
#    && apt-get install -y --force-yes unzip libaio-dev libmcrypt-dev git vim net-tools \
#    && apt-get clean -y
#
###</apache2>##
#ENV OPEN_SSL_NAME="openssl-1.0.2t"
#
##copy openssl to container
#COPY conf/$OPEN_SSL_NAME.tar.gz /opt/$OPEN_SSL_NAME.tar.gz
#
##update apache config
#COPY conf/apache/httpd.conf /usr/local/apache2/conf/httpd.conf
#COPY conf/apache/httpd-ssl.conf /usr/local/apache2/conf/extra/https-ssl.conf
#
##install new openssl
#RUN CFLAGS="-fPIC" && OPENSSL_VERSION="1.0.2t" \
#      && cd /opt \
#      && tar -xzf $OPEN_SSL_NAME.tar.gz \
#      && cd /opt/$OPEN_SSL_NAME \
#      && ./config shared && make && make install \
#      && rm -rf /opt/$OPEN_SSL_NAME
#
#ENV PHP_VERSION 5.3.29
#
#ENV PHP_INI_DIR /usr/local/lib
#RUN mkdir -p $PHP_INI_DIR/conf.d
#
## php 5.3 needs older autoconf
#RUN set -x \
#	&& apt-get update && apt-get install -y --force-yes autoconf2.13 && rm -r /var/lib/apt/lists/* \
#	&& curl --insecure -SLO http://launchpadlibrarian.net/140087283/libbison-dev_2.7.1.dfsg-1_amd64.deb \
#	&& curl --insecure -SLO http://launchpadlibrarian.net/140087282/bison_2.7.1.dfsg-1_amd64.deb \
#	&& dpkg -i libbison-dev_2.7.1.dfsg-1_amd64.deb \
#	&& dpkg -i bison_2.7.1.dfsg-1_amd64.deb \
#	&& rm *.deb \
#	&& curl --insecure -SL "http://php.net/get/php-$PHP_VERSION.tar.bz2/from/this/mirror" -o php.tar.bz2 \
#	&& curl --insecure -SL "http://php.net/get/php-$PHP_VERSION.tar.bz2.asc/from/this/mirror" -o php.tar.bz2.asc \
##	&& gpg --verify php.tar.bz2.asc \
#	&& mkdir -p /usr/src/php \
#	&& tar -xf php.tar.bz2 -C /usr/src/php --strip-components=1 \
#	&& rm php.tar.bz2* \
#	&& cd /usr/src/php \
#	&& ./buildconf --force \
#	&& ./configure --disable-cgi \
#		$(command -v apxs2 > /dev/null 2>&1 && echo '--with-apxs2' || true) \
#    --with-config-file-path="$PHP_INI_DIR" \
#    --with-config-file-scan-dir="$PHP_INI_DIR/conf.d" \
#		--with-mysql \
#		--with-mysqli \
#		--with-pdo-mysql \
#		--with-mbstring \
#		--with-soap \
##		--with-openssl-dir=/usr/local/ssl \
#		--with-openssl=/usr/local/ssl \
#		--with-openssl-dir=/usr/local/ssl \
#	&& make -j"$(nproc)" \
#	&& make install \
#	&& dpkg -r bison libbison-dev \
#	&& apt-get purge -y --force-yes --auto-remove autoconf2.13 \
#  && make clean
#
##fix bug openssl
#RUN echo -e '/usr/local/lib\n' >> /etc/ld.so.conf && ldconfig
#

#
## xdebug, if you want to debug
##RUN pecl install xdebug
#
## PHP composer
##RUN curl -sS https://getcomposer.org/installer | php --  --install-dir=/usr/bin --filename=composer
#
## apache configurations, mod rewrite
##RUN ln -s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/rewrite.load
#RUN a2enmod rewrite
#RUN a2enmod ssl
#RUN a2enmod headers
#RUN service apache2 restart