FROM centos:7

MAINTAINER nickma <nick.maiorsky@shipwire.com>

#
# Import the Centos-7 RPM GPG key to prevent warnings and Add EPEL Repository
#
RUN rpm --import http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-7 \
    && rpm --import http://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-7 \
    && rpm -Uvh http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-7.noarch.rpm \
	&& rpm -Uvh 


RUN yum -y install \
    mysql-devel \
    mysql-libs \
    mod_ssl \
    php56w \
    php56w-bcmath \
    php56w-cli \
    php56w-common \
    php56w-dba \
    php56w-devel \
    php56w-gd \
    php56w-imap \
    php56w-intl \
    php56w-ldap \
    php56w-mbstring \
    php56w-mcrypt \
    php56w-mysqlnd \
    php56w-odbc \
    php56w-opcache \
    php56w-pdo \
    php56w-pear \
    php56w-pecl-apcu \
    php56w-pecl-gearman \
    php56w-pecl-geoip \
    php56w-pecl-igbinary \
    php56w-pecl-imagick \
    php56w-pecl-memcache \
    php56w-pecl-redis \
    php56w-pecl-xdebug \
    php56w-process \
    php56w-snmp \
    php56w-soap \
    php56w-tidy \
    php56w-xml \
    php56w-xmlrpc \
    && yum -y update bash \
    && rm -rf /var/cache/yum/* \
    && yum clean all

#
# UTC Timezone & Networking
#
RUN ln -sf /usr/share/zoneinfo/UTC /etc/localtime \
    && echo "NETWORKING=yes" > /etc/sysconfig/network


#
# Global PHP configuration changes
#
RUN sed -i \
    -e 's~^;date.timezone =$~date.timezone = Europe/Rome~g' \
    -e 's~^;user_ini.filename =$~user_ini.filename =~g' \
    -e 's~^sendmail_path = /usr/sbin/sendmail -t -i$~sendmail_path = /usr/bin/msmtp -C /etc/msmtprc -t -i~g' \
    /etc/php.ini

#
# Jenkins Slave
#

ENV HOME /home/jenkins
RUN useradd -c "Jenkins user" -d $HOME -m jenkins

ARG VERSION=2.60

RUN curl --create-dirs -sSLo /usr/share/jenkins/slave.jar https://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/${VERSION}/remoting-${VERSION}.jar \
  && chmod 755 /usr/share/jenkins \
  && chmod 644 /usr/share/jenkins/slave.jar

COPY jenkins-slave /usr/local/bin/jenkins-slave

VOLUME /home/jenkins
WORKDIR /home/jenkins
USER jenkins

ENTRYPOINT ["jenkins-slave"]


#
# Purge
#

RUN rm -rf /sbin/sln \
    ; rm -rf /usr/{{lib,share}/locale,share/{man,doc,info,gnome/help,cracklib,il8n},{lib,lib64}/gconv,bin/localedef,sbin/build-locale-archive} \
    ; rm -rf /var/cache/{ldconfig,yum}/*

EXPOSE 22