# openldap-armv7

Latest release: 1.2.4 - OpenLDAP 2.4.47 -  [Osixia OpenLDAP](https://github.com/osixia/docker-openldap) | [Changelog](CHANGELOG.md) | [Docker Hub](https://hub.docker.com/r/osixia/openldap/) 

> OpenLDAP website : [www.openldap.org](http://www.openldap.org/)

# OpenLDAP

## Build
    
    $ mkdir openldap && cd openldap

    $ git clone https://github.com/osixia/docker-light-baseimage.git && 
    cd docker-light-baseimage &&
    sed -i '1s/debian:stretch-slim/balenalib\/raspberrypi3-debian:stretch/1' image/Dockerfile &&
    docker build -t light-baseimage:armv7 --rm image && 
    cd ..

    $ git clone https://github.com/adann0/openldap-armv7 &&
    cd openldap-armv7 &&
    docker build -t openldap:armv7 --rm image

## OpenLDAP Certificates

    $ cd .. && mkdir -p ssl/openldap && cd ssl/openldap
    
    $ openssl genrsa -des3 -out rootCA.key 4096
    $ openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 1024 -out rootCA.crt # => ldap.example.org
    $ openssl genrsa -out example.org.key 2048
    $ openssl req -new -key example.org.key -out example.org.csr # => 
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
	-p 389:389 \
	-p 636:636 \
	--env LDAP_ORGANISATION="Example TLD" \
	--env LDAP_DOMAIN="example.org" \
	--env LDAP_ADMIN_PASSWORD="secret" \
	--volume /home/nqqb/openldap/lib:/var/lib/ldap \
	--volume /home/nqqb/openldap/slapd:/etc/ldap/slapd.d \
	--hostname ldap.example.org \
	--volume /path/to/ssl/openldap:/container/service/slapd/assets/certs \
	--env LDAP_TLS_CRT_FILENAME=example.org.crt \
	--env LDAP_TLS_KEY_FILENAME=example.org.key \
	--env LDAP_TLS_CA_CRT_FILENAME=rootCA.crt \
	--detach openldap:armv7

    $ docker exec openldap ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w secret

# phpLDAPadmin

## Build

    $ git clone https://github.com/osixia/docker-web-baseimage.git &&
    cd docker-web-baseimage &&
    sed -i '1s/osixia\/light-baseimage:1.1.1/light-baseimage:armv7/1' image/Dockerfile &&
    sed -i '2d' image/Dockerfile &&
    docker build -t web-baseimage:armv7 --rm image

    $ cd .. && git clone https://github.com/osixia/docker-phpLDAPadmin.git &&
    cd docker-phpLDAPadmin &&
    sed -i '1s/osixia\/web-baseimage:1.1.1/web-baseimage:armv7/1' image/Dockerfile &&
    docker build -t phpldapadmin:armv7 --rm image

## Tests

    $ docker run \
	-p 6443:443 \
	--name phpldapadmin \
	--env PHPLDAPADMIN_LDAP_HOSTS=192.168.x.x \
	--volume /path/to/ssl/openldap:/container/service/ldap-client/assets/certs \
	--env PHPLDAPADMIN_LDAP_CLIENT_TLS_CA_CRT_FILENAME=rootCA.crt \
	--env PHPLDAPADMIN_LDAP_CLIENT_TLS_CRT_FILENAME=example.org.crt \
	--env PHPLDAPADMIN_LDAP_CLIENT_TLS_KEY_FILENAME=example.org.key \
	--volume /path/to/ssl/phpldapadmin:/container/service/phpldapadmin/assets/apache2/certs \
	--env PHPLDAPADMIN_HTTPS_CRT_FILENAME=example.org.crt \
	--env PHPLDAPADMIN_HTTPS_KEY_FILENAME=example.org.key \
	--env PHPLDAPADMIN_HTTPS_CA_CRT_FILENAME=rootCA.crt \
	--detach phpldapadmin:armv7

# More informations

    - https://github.com/osixia/docker-openldap
    - https://github.com/osixia/docker-phpLDAPadmin
    - https://gist.github.com/fntlnz/cf14feb5a46b2eda428e000157447309
