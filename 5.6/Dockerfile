FROM centos:7

MAINTAINER Nick Maiorsky <nick.maiorsky@shipwire.com>

#
# Import the Centos-7 RPM GPG key to prevent warnings and Add EPEL Repository
#
RUN rpm --import http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-7 \
    && rpm --import http://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-7 \
	&& rpm --import https://mirror.webtatic.com/yum/RPM-GPG-KEY-webtatic-el7 \
	&& rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm \
	&& rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm 

RUN yum -y install \
    jdk \
    boost-devel \
    mysql-devel \
    mysql-libs \
    mod_ssl \
    php56w \
    php56w-cli \
    php56w-common \
    php56w-dba \
    php56w-devel \
    && yum -y update bash \
    && rm -rf /var/cache/yum/* \
    && yum clean all

#
# UTC Timezone & Networking
#
RUN ln -sf /usr/share/zoneinfo/PST /etc/localtime \
    && echo "NETWORKING=yes" > /etc/sysconfig/network


#
# Global PHP configuration changes
#
RUN sed -i \
    -e 's~^;date.timezone =$~date.timezone = America/Los_Angeles~g' \
    -e 's~^;user_ini.filename =$~user_ini.filename =~g' \
    /etc/php.ini

#
# Jenkins Slave
#

USER root

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
USER root 

RUN rm -rf /sbin/sln \
    ; rm -rf /usr/{{lib,share}/locale,share/{man,doc,info,gnome/help,cracklib,il8n},{lib,lib64}/gconv,bin/localedef,sbin/build-locale-archive} \
    ; rm -rf /var/cache/{ldconfig,yum}/*
