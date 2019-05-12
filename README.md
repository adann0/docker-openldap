# openldap-armv7

Latest release: 1.2.4 - OpenLDAP 2.4.47 -  [Osixia OpenLDAP](https://github.com/osixia/docker-openldap) | [Changelog](CHANGELOG.md) | [Docker Hub](https://hub.docker.com/r/osixia/openldap/) 

> OpenLDAP website : [www.openldap.org](http://www.openldap.org/)

# OpenLDAP

## Build
    
    $ mkdir openldap && cd openldap

    $ git clone https://github.com/osixia/docker-light-baseimage.git && 
    cd docker-light-baseimage &&
    sed '1s/debian:stretch-slim/balenalib\/raspberrypi3-debian:stretch/1' image/Dockerfile &&
    docker build -t light-baseimage:armv7 --rm image && 
    cd ..

    $ git clone https://github.com/adann0/openldap-armv7 &&
    cd openldap-armv7 &&
    docker build -t openldap:armv7 --rm image

## OpenLDAP Certificates

    $ cd .. && mkdir -p ssl/openldap && cd ssl/openldap
    
    $ openssl genrsa -des3 -out rootCA.key 4096
    $ openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt
    $ openssl genrsa -out example.org.key 2048
    $ openssl req -new -key example.org.key -out example.org.csr
    $ openssl req -new -sha256 \
    -key example.org.key \
    -subj "/C=US/ST=CA/O=MyOrg, Inc./CN=example.org" \
    -reqexts SAN \
    -config <(cat /etc/ssl/openssl.cnf \
        <(printf "\n[SAN]\nsubjectAltName=DNS:example.org,DNS:www.example.org")) \
    -out example.org.csr
    $ openssl req -in example.org.csr -noout -text
    $ openssl x509 -req -in example.org.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out example.org.crt -days 500 -sha256
    $ openssl x509 -in example.org.crt -text -noout

## Tests

    $ docker run \
    --name openldap \
    --volume <lib_path>:/var/lib/ldap \
    --volume <slapd.d_path>:/etc/ldap/slapd.d \
    --hostname ldap.example.org \
    --volume /path/to/ssl/openldap:/container/service/slapd/assets/certs \
    --env LDAP_TLS_CRT_FILENAME=example.org.crt \
    --env LDAP_TLS_KEY_FILENAME=example.org.key \
    --env LDAP_TLS_CA_CRT_FILENAME=rootCA.crt \
    --detach openldap:armv7

    $ docker exec openldap ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin

# phpLDAPadmin

## Build

    $ git clone https://github.com/osixia/docker-web-baseimage.git &&
    cd docker-web-baseimage &&
    sed -i '1s/osixia\/light-baseimage:1.1.1/light-baseimage:armv7/1' image/Dockerfile &&
    docker build -t web-baseimage:armv7 --rm image

    $ cd .. && git clone https://github.com/osixia/docker-phpLDAPadmin.git &&
    cd docker-phpLDAPadmin &&
    sed -i '1s/osixia\/web-baseimage:1.1.1/web-baseimage:armv7/1' image/Dockerfile &&
    docker build -t phpldapadmin:armv7 --rm image

## phpLDAPadmin Certificates

    $ cd .. && mkdir -p phpldapadmin/apache && cd phpldapadmin/apache
    $ openssl req -new -x509 -days 365 -nodes -out phpldapadmin.crt -keyout phpldapadmin.key
    $ openssl dhparam -out dhparams.pem 2048

# Tests

# More informations

    - https://github.com/osixia/docker-openldap
    - https://github.com/osixia/docker-phpLDAPadmin
    - https://gist.github.com/fntlnz/cf14feb5a46b2eda428e000157447309
