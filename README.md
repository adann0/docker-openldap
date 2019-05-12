# openldap-armv7

Latest release: 1.2.4 - OpenLDAP 2.4.47 -  [Osixia OpenLDAP](https://github.com/osixia/docker-openldap) | [Changelog](CHANGELOG.md) | [Docker Hub](https://hub.docker.com/r/osixia/openldap/) 

**A docker image to run OpenLDAP on Armv7.**

> OpenLDAP website : [www.openldap.org](http://www.openldap.org/)

# Steps

    $ cd && mkdir openldap && cd openldap

    $ git clone https://github.com/osixia/docker-light-baseimage.git && 
    cd docker-light-baseimage &&
    sed '1s/debian:stretch-slim/balenalib\/raspberrypi3-debian:stretch/1' image/Dockerfile &&
    docker build -t light-baseimage:armv7 --rm image

    $ cd .. && git clone https://github.com/adann0/openldap-armv7 &&
    cd openldap-armv7 &&
    docker build -t openldap:armv7 --rm image

# You need to generate your own SSL Certificate : https://gist.github.com/fntlnz/cf14feb5a46b2eda428e000157447309
    
    
# Sources :
    - https://gist.github.com/fntlnz/cf14feb5a46b2eda428e000157447309
