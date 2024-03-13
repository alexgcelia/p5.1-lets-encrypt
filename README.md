# p5.1-lets-encrypt
Este repositorio es para la práctica05.1 de docker-compose del módulo IAW

# HTTPS con Let’s Encrypt, Docker y Docker Compose.
## Trabajaremos con un archivo docker-compose.yml
### Este es el archivo con la instalación automatizada de nuestro prestashop/prestashop
#### Aqui encontramos la instalación de Wordpress, como podemos comprobar en ella irán las variables que escribirmeos en nuestro archivo .env
```
version: '3.4'

services:
  mysql:
    image: mysql
    #command: --default-authentication-plugin=mysql_native_password
    ports: 
      - 3306:3306
    environment: 
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=${MYSQL_DATABASE}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    volumes: 
      - mysql_data:/var/lib/mysql
    networks: 
      - backend-network
    security_opt:
      - seccomp:unconfined
    restart: always
```

#### Aqui encontramos la instalación de MySQL
```
phpmyadmin:
    image: phpmyadmin
    ports:
      - 8080:80
    environment: 
      - PMA_ARBITRARY=1
    networks: 
      - backend-network
      - frontend-network
    restart: always
    depends_on: 
      - mysql
```

#### Aqui encontramos la instalación de MySQL
```
prestashop:
    image: prestashop/prestashop:8
    environment: 
      - DB_SERVER=mysql
      - DB_USER=${DB_USER}
      - DB_PASSWD=${DB_PASSWD}
      - DB_PREFIX=${DB_PREFIX}
      - DB_NAME=${DB_NAME}
      - PS_DOMAIN=${PS_DOMAIN}
      - PS_LANGUAGE=${PS_LANGUAGE}
      - PS_COUNTRY=${PS_COUNTRY}
      - PS_ENABLE_SSL=1
      - PS_INSTALL_AUTO=1
      - ADMIN_MAIL=${ADMIN_MAIL}
      - ADMIN_PASSWD=${ADMIN_PASSWD}
    volumes:
      - prestashop_data:/var/www/html
    networks: 
      - backend-network
      - frontend-network
    restart: always
    depends_on: 
      - mysql
```

#### Aquí encontramos la instalación de https en wordpress (let's encrypt)
```
https-portal:
    image: steveltn/https-portal:1
    ports:
      - 80:80
      - 443:443
    restart: always
    environment:
      DOMAINS: '${DNS_DOMAIN_SECURE} -> http://prestashop:80'
      STAGE: 'production'
    networks:
      - frontend-network
```

#### Aquí encontramos los volúmenes y redes creadas del fichero
```
volumes:
  mysql_data:
  prestashop_data:

networks: 
  backend-network:
  frontend-network:
```

## También trabajremos con un archivo enviroment (.env)
### Este contiene información sobre la instalación automatizada
#### Aquí escribimos nuestro dominio anteriormente creado en NoIP
```
DNS_DOMAIN_SECURE=alexg-letsencrypt.ddns.net
```
#### Aquí encontramos las variables de MySQL
```
MYSQL_ROOT_PASSWORD=root
MYSQL_DATABASE=prestashop
MYSQL_USER=ps_user
MYSQL_PASSWORD=admin
```

#### Aquí escribiremos las variables para nuestro Prestashop
```
DB_USER=ps_user
DB_PASSWD=admin
DB_PREFIX=ps_
DB_NAME=prestashop
PS_DOMAIN=alexg-letsencrypt.ddns.net
PS_LANGUAGE=es
PS_COUNTRY=ES
ADMIN_MAIL=demo@prestashop.com
ADMIN_PASSWD=prestashop_demo
```
