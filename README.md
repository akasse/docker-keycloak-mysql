# DEV OPS BY AKKASE
# Installation et configuration de keyloack 

docker tag local-image:tagname new-repo:tagname
docker tag akasse/devops-keyloack:1.0 akasse/devops-keyloack:1.0
docker tag akasse/devops-keyloack:1.0 akasse/devops-keyloack:1.0

docker push new-repo:tagname
docker push akasse/devops-keyloack:1.0

# pipeline syntaxe
tool name: 'Maven', type: 'maven'

# authentification
username: akasse
passeword: ilatouba


Environment variables
Generic variable names can be used to configure any Database type, defaults may vary depending on the Database.

DB_ADDR: Specify hostname of the database (optional)
DB_PORT: Specify port of the database (optional, default is DB vendor default port)
DB_DATABASE: Specify name of the database to use (optional, default is keycloak).
DB_SCHEMA: Specify name of the schema to use for DB that support schemas (optional, default is public on Postgres).
DB_USER: Specify user to use to authenticate to the database (optional, default is keycloak).
DB_USER_FILE: Specify user to authenticate to the database via file input (alternative to DB_USER).
DB_PASSWORD: Specify user's password to use to authenticate to the database (optional, default is password).
DB_PASSWORD_FILE: Specify user's password to use to authenticate to the database via file input (alternative to DB_PASSWORD).


====================================================

version: "2"
services:

    mariadb:
        image: mariadb:10.4.4
        container_name: ak_mariadb
        labels:
            - com.akasse.description= devops with keycloak by akasse"
            - com.akasse.username=${KEYCLOACK_ADMIN}
            - com.akasse.password=${KEYCLOACK_PASSWORD}
            - com.akasse.email=${KEYCLOACK_AUTOR}
        #restart: always
        environment:
            - MYSQL_ROOT_PASSWORD=${DB_PASSWORD}  
            - MYSQL_USER=${DB_USER}
            - MYSQL_DATABASE=${DB_DATABASE}
            - MYSQL_PASSWORD=${DB_PASSWORD}  
        volumes:
            - ./mariadb-data:/var/lib/mysql
        ports:
            - '3306:3306'
    adminer:
        image: adminer:4.7.1
        container_name : ${ADMINER_NAME}
        hostname: ${ADMINER_HOSTNAME}
        labels:
            - com.akasse.description= devops with ADMINER by akasse"
            - com.akasse.email=${ADMINER_AUTOR}
        #restart: always
        depends_on:
            - mariadb
        links:
            - mariadb:mariadb
        ports:
            - 2020:8080

    keycloak:
        image: jboss/keycloak:6.0.0
        container_name : ${KEYCLOACK_NAME}
        hostname: ${KEYCLOACK_HOSTNAME}
        labels:
            - com.akasse.description= devops with keycloak by akasse"
            - com.akasse.username=${KEYCLOACK_ADMIN}
            - com.akasse.password=${KEYCLOACK_PASSWORD}
            - com.akasse.email=${KEYCLOACK_AUTOR}
        environment:
            - KEYCLOAK_USER=${KEYCLOACK_ADMIN}
            - KEYCLOAK_PASSWORD=${KEYCLOACK_PASSWORD}
            - KEYCLOAK_IMPORT=/tmp/example-realm.json 
            - DB_ADDR=${DB_PORT}
            - DB_PORT=${DB_PORT}
            - DB_DATABASE=${DB_DATABASE}
            - DB_SCHEMA=${DB_DATABASE}
            - DB_USER=${DB_USER}
            - DB_PASSWORD=${DB_PASSWORD}
        volumes:
            - ./keycloak-data/example-realm.json:/tmp/example-realm.json
        ports:
            - 2015:8080   # Web interface
        links:
            - mariadb:mariadb

