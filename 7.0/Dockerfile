FROM centos:7

MAINTAINER Nick Maiorsky <nick.maiorsky@shipwire.com>


USER root

RUN aws s3 cp --region us-west-2 s3://sws-artifacts-store/go-binaries/rtt /usr/local/bin
