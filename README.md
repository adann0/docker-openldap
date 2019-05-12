# openldap-armv7

Latest release: 1.2.4 - OpenLDAP 2.4.47 -  [Osixia OpenLDAP](https://github.com/osixia/docker-openldap) | [Changelog](CHANGELOG.md) | [Docker Hub](https://hub.docker.com/r/osixia/openldap/) 

**A docker image to run OpenLDAP on Armv7.**

> OpenLDAP website : [www.openldap.org](http://www.openldap.org/)

# Steps

    $ git clone https://github.com/osixia/docker-light-baseimage.git && cd docker-light-baseimage
    $ sed '1s/debian:stretch-slim/balenalib\/raspberrypi3-debian:stretch/1' image/Dockerfile
    $ docker build -t light-baseimage:armv7 --rm image
