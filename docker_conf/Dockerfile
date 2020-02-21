FROM debian:jessie

WORKDIR /var/www/html

RUN echo "alias ll='ls -la --color'" | tee --append /etc/bash.bashrc
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get -y update && apt-get install -y apt-utils libc6 debconf subversion
RUN apt-get install -y sudo man git htop vim mc nano \
    software-properties-common \
    apt-transport-https lsb-release wget lynx telnet  \
    parallel bzip2 acl gnupg \
    libxml2-dev libssl-dev curl

RUN adduser --gecos '' --uid 1000 --gid 50 --disabled-password php \
    && adduser php staff

RUN sudo apt-get install -y
RUN echo "deb http://packages.dotdeb.org jessie all" >> /etc/apt/sources.list.d/dotdeb.org.list && \
    echo "deb-src http://packages.dotdeb.org jessie all" >> /etc/apt/sources.list.d/dotdeb.org.list && \
    wget -O- http://www.dotdeb.org/dotdeb.gpg | apt-key add -
RUN echo "deb https://packages.sury.org/php/ jessie main" | tee /etc/apt/sources.list.d/php.list
RUN apt-get update
RUN apt-get install -y php7.4 php7.4-fpm php7.4-cli php7.4-mbstring  php7.4-intl php7.4-zip php7.4-mysql php7.4-redis php7.4-memcache php7.4-gd php7.4-curl php7.4-xsl php7.4-mongodb php7.4-dev php-pear php7.4-soap php7.4-pgsql php7.4-sqlite3 --force-yes
RUN sed -i "s/^;\(date\.timezone\(\s*\)\?=\).*/\1 Europe\/Istanbul/" /etc/php/7.4/cli/php.ini
RUN sed -i "s/^;\(date\.timezone\(\s*\)\?=\).*/\1 Europe\/Istanbul/" /etc/php/7.4/fpm/php.ini
RUN sed -i "s/^\(memory_limit\(\s*\)\?=\).*/\1 4G/" /etc/php/7.4/cli/php.ini
RUN sed -i "s/^\(memory_limit\(\s*\)\?=\).*/\1 256M/" /etc/php/7.4/fpm/php.ini
RUN sed -i "s/^\(short_open_tag\(\s*\)\?=\).*/\1 On/" /etc/php/7.4/fpm/php.ini
RUN sed -i "s/^\(short_open_tag\(\s*\)\?=\).*/\1 On/" /etc/php/7.4/fpm/php.ini


RUN echo -e "; priority=20\nextension=pdo_sqlite3.so" > /etc/php/7.4/mods-available/pdo_sqlite3.ini
RUN echo -e "; priority=20\nextension=pdo_sqlsrv.so" > /etc/php/7.4/mods-available/pdo_sqlsrv.ini

RUN phpenmod -v 7.4 -s ALL mbstring intl pdo_mysql redis memcache gd curl xml xsl mongodb sqlsrv pdo_sqlsrv zip pdo_pgsql pdo_sqlite3
RUN apt-get -y install supervisor nginx
RUN apt-get -y update && apt-get -y dist-upgrade --force-yes

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
RUN rm -rf /var/www/html
ADD . /var/www/html
RUN chown -R php.staff /var/www/html

#external file
ADD docker-run.sh /docker-run.sh
RUN chmod 755 /docker-run.sh
RUN /docker-run.sh

EXPOSE 9000 80 443
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]