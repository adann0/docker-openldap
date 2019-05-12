# openldap-armv7

Latest release: 1.2.4 - OpenLDAP 2.4.47 -  [Osixia OpenLDAP](https://github.com/osixia/docker-openldap) | [Changelog](CHANGELOG.md) | [Docker Hub](https://hub.docker.com/r/osixia/openldap/) 

**A docker image to run OpenLDAP on Armv7.**

> OpenLDAP website : [www.openldap.org](http://www.openldap.org/)

# Build

    $ git clone https://github.com/osixia/docker-light-baseimage.git && 
    cd docker-light-baseimage &&
    sed '1s/debian:stretch-slim/balenalib\/raspberrypi3-debian:stretch/1' image/Dockerfile &&
    docker build -t light-baseimage:armv7 --rm image

    $ git clone https://github.com/adann0/openldap-armv7 &&
    cd openldap-armv7 &&
    docker build -t openldap:armv7 --rm image

# You need to generate your own SSL Certificate : https://gist.github.com/fntlnz/cf14feb5a46b2eda428e000157447309

# Tests

    $ docker run \
    --volume /home/nqqb/openldap/lib:/var/lib/ldap \
    --volume /home/nqqb/openldap/slapd:/etc/ldap/slapd.d \
    --hostname ldap.example.com \
    --volume /home/nqqb/ssl:/container/service/slapd/assets/certs \
    --env LDAP_TLS_CRT_FILENAME=example.com.crt \
    --env LDAP_TLS_KEY_FILENAME=example.com.key \
    --env LDAP_TLS_CA_CRT_FILENAME=rootCA.crt \
    --detach openldap:armv7


