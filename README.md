# docker-resources-master
Notas del curso [Docker, de principiante a experto](https://www.udemy.com/course/docker-de-principiante-a-experto/ "Docker, de principiante a experto") en Udemy

# Instalar Docker
Config file /lib/systemd/system/docker.service

## CentOS
---------
#### Utilidades
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
#### Agregar el repo de docker
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
#### Instalar docker
sudo yum install docker-ce -y
#### Iniciar el servicio
sudo systemctl start docker
#### Iniciarlo con el sistema
sudo systemctl enable docker
#### Agregar usuario al grupo docker 
whoami # Saber el nombre de tu usuario
sudo usermod -aG docker nombre_de_salida_en_whoami
#### Salir de la sesión
exit
#### Iniciar de nuevo con el usuario y probar 
docker run hello-world

## Fedora 
---------
##### La instalación es igual que en CentOS, solo deben modificar la url del repo, porque los pasos son idénticos
#### Utilidades
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
#### Agregar el repo de docker
sudo yum-config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
#### Instalar docker
sudo yum install docker-ce -y
#### Iniciarlo con el sistema
sudo systemctl enable docker
#### Agregar usuario al grupo docker 
whoami # Saber el nombre de tu usuario
sudo usermod -aG docker nombre_de_salida_en_whoami
#### Salir de la sesión
exit
#### Iniciar de nuevo con el usuario y probar 
docker run hello-world

## Ubuntu
---------
#### Actualiza los repos
sudo apt-get update
#### Instala utilidades
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y
#### Agregar el gpg 
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
#### Agregar el repo
 sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
#### Actualizar de nuevo
 sudo apt-get update
#### Instalar docker
 sudo apt-get install docker-ce
#### Iniciarlo con el sistema
sudo systemctl enable docker
#### Agregar usuario al grupo docker 
whoami # Saber el nombre de tu usuario
sudo usermod -aG docker nombre_de_salida_en_whoami
#### Salir de la sesión
exit
#### Iniciar de nuevo con el usuario y probar 
docker run hello-world

## Debian
---------
### Actualiza los repos
sudo apt-get update
#### Instala utilidades
sudo apt-get install apt-transport-https ca-certificates curl gnupg2 software-properties-common -y
#### Agregar el gpg 
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
#### Agregar el repo
 sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
#### Actualizar de nuevo
 sudo apt-get update
#### Instalar docker
 sudo apt-get install docker-ce
#### Iniciarlo con el sistema
sudo systemctl enable docker
#### Agregar usuario al grupo docker 
whoami # Saber el nombre de tu usuario
sudo usermod -aG docker nombre_de_salida_en_whoami
#### Salir de la sesión
exit
#### Iniciar de nuevo con el usuario y probar 

------------

## docker run hello-world

## Dockerfile:
- FROM
- RUN
- COPY/ADD
- ENV
- WORKDIR
- EXPOSE
- LABEL
- USER
- VOLUME
- CMD
- DOCKERIGNORE

## COMANDOS
docker build -t nombreDeLaImagen .
docker ps
docker rm -f nombreDelContenedor
docker run -d -p 80:80 nombreDeLaImagen

## ejemplo 1 Dockerfile====
FROM ubuntu:20.04
LABEL version=1.0
LABEL description="This is an apache image"
LABEL vendor=yo
RUN DEBIAN_FRONTEND="noninteractive" apt update
RUN DEBIAN_FRONTEND="noninteractive" apt install apache2 -y
WORKDIR /var/www/html
COPY startbootstrap-freelancer-gh-pages .
ENV contenido prueba
RUN echo "$contenido" > /var/www/html/prueba.html
EXPOSE 8080
CMD apachectl -D FOREGROUND

## ejemplo2 Dockerfile======
FROM ubuntu:20.04
LABEL version=1.0
LABEL description="This is an apache image"
LABEL vendor=yo
RUN DEBIAN_FRONTEND="noninteractive" apt update
RUN DEBIAN_FRONTEND="noninteractive" apt install apache2 -y
COPY startbootstrap-freelancer-gh-pages /var/www/html
RUN echo "$(whoami)" > /var/www/html/user1.html
RUN useradd leonel
RUN chown leonel /var/www/html -R
USER leonel
RUN echo "$(whoami)" > /tmp/user2.html
VOLUME /var/www/html
USER root
RUN cp /tmp/user2.html /var/www/html/user2.html
COPY run.sh /run.sh
CMD sh /run.sh

## run.sh========
#!/bin/bash
echo "Iniciando container..."
echo "INICIADO!!" > /var/www/html/ini.html
apachectl -D FOREGROUND

## .dockerignore====
startbootstrap-freelancer-gh-pages.zip:Zone.Identifier
startbootstrap-freelancer-gh-pages.zip

## ejemplo 3 Dockerfile=====
FROM nginx
RUN useradd leonel
COPY startbootstrap-freelancer-gh-pages /usr/share/nginx/html
ENV archivo docker
WORKDIR /usr/share/nginx/html
RUN echo "$archivo" > /usr/share/nginx/html/env.html
EXPOSE 90
LABEL version=1
USER leonel
RUN echo "Yo soy $(whoami)" > /tmp/yo.html
USER root
RUN cp /tmp/yo.html /usr/share/nginx/html/docker.html
VOLUME /var/log/nginx
CMD nginx -g 'daemon off;'

## Buenas practicas=====
Efimeros
Un servicio por contenedor
Build context-> .dockerignore
Pocas capas
Multilinea\
Varios argumentos en una sola capa
No instalar paquetes innecesarios
Labels

## instalar https SSL=====
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mysitename.key -out mysitename.crt

## ejemplo 4 Dockerfile con php y ssl =====
FROM centos:7
RUN \
  yum -y install \
   httpd \
   php \
   php-cli \
   php-common \
   mod_ssl \
   openssl
RUN echo "<?php phpinfo(); ?>" > /var/www/html/hola.php
COPY startbootstrap-sb-admin-2 /var/www/html/
COPY ssl.conf /etc/httpd/conf.d/default.conf
COPY docker.crt /docker.crt
COPY docker.key /docker.key
EXPOSE 443
CMD apachectl -DFOREGROUND

## ssl.conf ======
<VirtualHost *:443>
 ServerName localhost
 DocumentRoot /var/www/html
 SSLEngine on
 SSLCertificateFile /docker.crt
 SSLCertificateKeyFile /docker.key
</VirtualHost>

## eliminar imagen ======
docker rmi centos:prueba1
docker rmi 3e65db2bd7b4

## cambiar el nombre del dockerfile ===
vi my-dockerfile
docker build -t test -f my-dockerfile .

## eliminar images dangling ====
docker images -f dangling=true -q | xargs docker rmi

# ========CONTENEDORES=======
docker run -d -p 8080:8080 jenkins/jenkins  //-d corre en 2do plano

## cambiar nombre====
docker rename crazy_heisenberg jenkins

## detener(por nombre o id) ===
docker stop 63f473cc8d80

## iniciar(nombre o id) ====
docker start jenkins

## reiniciar ===
docker restart jenkins

## entrar en la consola interactiva ===
docker exec -ti jenkins bash
docker exec -u root -ti jenkins bash      <- con usuario root

## eliminar todos los contenedores ==
docker ps -q | xargs docker rm -f
docker rm -fv $(docker ps -aq)

## agregar una variable de entorno ===
docker run -dti -e "prueba1=4321" --name environment env

## ver todas las variables de entorno en exec ti ===
env

## conectarse a mysql =====
docker pull mysql
docker run -d --name my-db1 -e "MYSQL_ROOT_PASSWORD=12345678" mysql:5.7
docker logs -f my-db1
sudo apt-get update
sudo apt-get install mysql-client -y
docker inspect my-db1
docker exec -it my-db1 mysql -u root -p12345678
-o-
docker run -d -p 3333:3306 --name my-db2 -e "MYSQL_ROOT_PASSWORD=12345678" -e "MYSQL_DATABASE=docker-db" -e "MYSQL_USER=docker-user" -e "MYSQL_PASSWORD=87654321" mysql:5.7
mysql -u root -p12345678 -h 127.0.0.1 --port 3333

## mongo ======
docker pull mongo
docker run -d --name my-mongo -p 27017:27017 mongo

## limitar recursos ===
 docker run -d -m "500mb" --name mongo2 mongo
docker run -d -m "500mb" --cpuset-cpus 0-1 --name mongo3 mongo

## saber cpu ====
grep "model name" /proc/cpuinfo
grep "model name" /proc/cpuinfo | wc -l

## copiar archivos al contenedor y viceversa ===
docker cp index.html apache:/usr/local/apache2/htdocs
docker cp apache:/var/log/dpkg.log .

## convertir un contenedor en una imagen ===
docker commit centos centos-resultante
//OJO: lo que este adentro de /volumen no se guarda 

## sobreescribir el CMD de una imagen sin dockerfile ==
docker run -d -p 8080:8080 centos:7 python -m SimpleHTTPServer 8080

## destruir contenedor automaticamente ===
docker run --rm -ti --name centos centos bash 

## cambiar el documento root ====
docker info | grep -i root
en vi /lib/systemd/system/docker.service al final de la linea ExecStar agregar --data-root /opt/docker
systemctl daemon-reload
systemctl restart docker
sudo systemctl stop docker
rm -rf /opt/*
cd /var/lib
mv docker/ /opt/
ls /opt/docker/
systemctl daemon-reload
systemctl restart docker

## ver informacion detallada del contenedor ===
docker inspect nameContainer

# =========VOLUMENES=========
## Para almacenar datos persistentes
Host
Anonymus
Named Volumes

## hacer dump en mysql ===
mysqldump -u root --column-statistics=0 -h 127.0.0.1 -p --port 3307 sys > dump.sql
mysql -u root -h 127.0.0.1 -p docker-db < dump.sql --port 3307

## volume host ====
cd /opt/
mkdir mysql
docker run -d -p 3307:3306 --name db -e "MYSQL_ROOT_PASSWORD=12345678" -v /opt/mysql/:/var/lib/mysql mysql:5.7

## volumen anonimo ===
docker run -d -p 3307:3306 --name mysql -e "MYSQL_ROOT_PASSWORD=12345678" -e "MYSQL_DATABASE=docker-db" -v /var/lib/mysql mysql:5.7
se guarda en var/lib/docker/volumes o en 
/mnt/docker/data/docker/volumes
https://es.stackoverflow.com/questions/544280/no-encuentro-la-ruta-var-lib-docker-volumes-en-mi-linux-docker

## encontrar carpet docker/volumes ===
Para Docker Engine v20.10.16: \\wsl$\docker-desktop-data\data\docker\volumes
¿Cómo se accede desde WSL?
En Windows CMD ejecutas net use h: \\wsl$\docker-desktop-data (crear un drive h apuntando a \wsl$\docker-desktop-data)
En terminal de Ubuntu ejecutas
sudo mkdir /mnt/docker
sudo mount -t drvfs h: /mnt/docker (crear un directorio /mnt/docker y montar h a /mnt/docker )
Ahora cd /mnt/docker/data/docker/volumes ya tienes todos

## eliminar volume ===
docker volume rm volume_name volume_name
docker volume ls -f dangling=true
docker volume prune
o:
docker volume ls -f dangling=true -q | xargs docker volume rm

## crear volumen ===
docker volume create namevolume

## volumen nombrado ===
docker run -d -p 3307:3306 --name mysql -e "MYSQL_ROOT_PASSWORD=12345678" -e "MYSQL_DATABASE=docker-db" -v mysql-data:/var/lib/mysql mysql:5.7

## conectarse a mongo =====
docker exec -ti my-mongo mongosh

## persistiendo data en jenkins ========
4c381a40a4df4ce1adafee00d2b11a9f

## ejecutas comando desde exec sin entrar ====
docker exec jenkins bash -c "cat /var/jenkins_home/secrets/initialAdminPassword"

## comando para saber la hora ==========
date +%H:%M:%S

## compartir volumenes entre contenedores ======
mkdir volumen
cd volumen/
mkdir common
vi Dockerfile:
FROM centos
COPY start.sh /start.sh
RUN chmod +x /start.sh
CMD /start.sh

## vi start.sh:
#!/bin/bash
while true; do
  echo "<p> $(date +%H:%M:%S) </p>" >> /opt/index.html
  sleep 10
done

docker build -t generador .
docker run -v $PWD/common:/opt -d --name gen generador
docker run -d --name nginx -v $PWD/common:/usr/share/nginx/html -p 80:80 nginx

# ===== REDES ====
Tipos: brige, host, none, overlay

## interfaz nueva ==========
ip a | grep docker 

## ver todas las redes =========
docker network ls

## ver informacion de una red ======
docker network inspect bridge

## hacer ping entre contenedores ======
docker exec busy_chebyshev bash -c "ping 172.17.0.3"

## crear red ============
docker network create test-network
docker network create -d bridge --subnet 172.124.10.0/24 --gateway 172.124.10.1 docker-test-network

## agregar contenedor a una red =========
docker network create test1
docker run -d --network test1 nginx

## conectar redes en la misma red ===========
docker run -d --network docker-test-network --name cont1  -ti ce
ntos
docker run -d --network docker-test-network --name cont2  -ti ce
ntos
docker exec cont2 bash -c "ping cont1"

## conectar contenedores en distintas redes ===========
docker network connect docker-test-network cont3
docker network disconnect docker-test-network cont3

## eliminar red =======
docker network rm docker-test-network

## asignar ip a contenedor ========
docker network create --subnet 172.128.10.0/24 --gateway 172.128.10.1 -d bridge my-net
docker run --network my-net --ip 172.128.10.50 -d --name nginx2 -ti centos

## red host ==========
docker run --network host -d --name test2 -ti centos

## contenedor sin red ======
 docker run --network none --name hola -d -ti centos

## puerto solo con localhost ======
docker run -d -p 127.0.0.1:8081:80 nginx

# ===== COMPOSE =====
aplicaciones multicontenedor
contenedores, imagenes, volumenes, redes

## documentacion =========
https://docs.docker.com/compose/compose-file/

## instalarlo ==========
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose

## primeros pasos ========
mkdir ~/docker-compose
cd ~/docker-compose
nano docker-compose.yml :
version: '3'
services:
  web:
    container_name: nginx1
    ports:
      - "8080:80"
    image: nginx

## levantarlo y eliminarlo ========
docker-compose up -d
docker-compose down

## ejemplo 2 docker-compose.yml ====
version: '3'
services:
  db:
    image: mysql:5.7
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      - "MYSQL_ROOT_PASSWORD=12345678"

## archivo de variables de entorno ======
echo "MYSQL_ROOT_PASSWORD=12345678" > common.env
version: '3'
services:
  db:
    image: mysql:5.7
    container_name: mysql
    ports:
      - "3306:3306"
    env_file: common.env

## volumen nombrado =========
version: '3'
services:
  web:
    container_name: nginx1
    ports:
      - "8080:80"
    volumes:
      - "vol2:/usr/share/nginx/html"
    image: nginx
volumes:
  vol2:

## volumen host =========
version: '3'
services:
  web:
    container_name: nginx2
    ports:
      - "8081:80"
    volumes:
      - "/home/leovale/docker-compose/html:/usr/share/nginx/html"
    image: nginx

## redes ============
version: '3'
services:
  web:
    container_name: nginx2
    ports:
      - "8081:80"
    image: httpd
    networks:
      - net-test
  web2:
    container_name: nginx3
    ports:
      - "8082:80"
    image: httpd
    networks:
      - net-test
networks:
  net-test:

## construir imagen en compose ========
version: '3'
services:
  web:
    container_name: web
    image: web-test
    build: .

> NOTA: el Dockerfile debe estar en la misma carpeta

## construir imagen con otro nombre =========
version: '3'
services:
  web:
    container_name: web
    image: web-test
    build:
      context: .
      dockerfile: Dockerfile1


## instalar ping =========
apt-get update && apt-get install -y iputils-ping

## sobreescribir cmd =========
version: '3'
services:
  web:
    image: centos:7
    command: python -m SimpleHTTPServer 8080
    ports:
      - "8080:8080"

## cambiar nombre a un docker-compose.yml ===
docker-compose -f docker-compose-cmd.yml up -d

## limitar memoria =========
version: '2'
services:
  web:
    container_name: nginx
    mem_limit: 20m
    cpuset: "0"
    image: nginx

## politica de reinicio no =========
#### docker-compose.yml :
version: '3'
services:
  test:
    container_name: test
    image: restart-image
    build: .
\#    restart: no

#### Dockerfile :
FROM centos
COPY start.sh /start.sh
RUN chmod +x /start.sh
CMD /start.sh

#### start.sh :
\#!/bin/bash
echo "Estoy vivo"
sleep 5
echo "Estoy detenido"

docker-compose build   <-- para crear la imagen 
docker-compose up -d   <-- para crear el conetenedor
docker logs -f test :
Estoy vivo
Estoy detenido

## restart always ===========
docker logs -f test :
Estoy vivo
Estoy detenido
Estoy vivo
Estoy detenido
Estoy vivo
Estoy detenido

## ejecutar docker ps varias veces ===========
watch -d docker ps

## unless-stopped ========
Similar a always, excepto que cuando el contenedor se detiene (manualmente o de otra manera), no se reinicia incluso después de que se reinicia el demonio Docker.

## on-failure[:max-retries] ======
Reinicie el contenedor si sale debido a un error, que se manifiesta como un código de salida distinto de cero. Opcionalmente, limite la cantidad de veces que el demonio de Docker intenta reiniciar el contenedor usando la :max-retriesopción.
start.sh :
echo "Estoy vivo"
sleep 5
echo "Estoy detenido"
exit 1

El siguiente ejemplo inicia un contenedor de Redis y lo configura para que siempre se reinicie a menos que se detenga explícitamente o se reinicie Docker.
##### docker run -d --restart unless-stopped redis
Este comando cambia la política de reinicio de un contenedor que ya se está ejecutando llamado redis
docker update --restart unless-stopped redis

## personalizar el nombre del proyecto ====
- p   <-- --project-name NAME 
ejemplo:
docker-compose -p webtest -f docker-compose-cmd.yml up -d

## servicio depende de otro ===
depends_on:
\      - db

## ver los procesos del contenedor actual =====
docker-compose logs -f

## wordpress+mysql docker-compose.yml =====
version: '3'
services:
  db:
    container_name: wp-mysql
    image: mysql:5.7
    volumes:
       - $PWD/data:/var/lib/mysql
    environment:
       MYSQL_ROOT_PASSWORD: 12345678
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress
    ports:
      - "3306:3306"
    networks:
      - my_net
  wp:
    container_name: wp-web
    volumes:
      - "$PWD/html:/var/www/html"
    depends_on:
      - db
    image: wordpress
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
    networks:
      - my_net
networks:
  my_net:

## drupal + postgres docker-compose.yml =====
version: '3'
services:
  drupal:
    volumes:
      - drupal:/var/www/html
    image: drupal:8-apache
    ports:
      - 80:80
    networks:
      - net
  postgres:
    image: postgres:10
    environment:
      POSTGRES_PASSWORD: example
    volumes:
      - $PWD/data:/var/lib/postgresql/data
    networks:
      - net
volumes:
  drupal:
networks:
  net:

## prestashop + mysql docker-compose.yml ====
version: '3'
services:
  db:
    container_name: ps-mysql
    image: mysql:5.7
    volumes:
       - $PWD/data:/var/lib/mysql
    environment:
       MYSQL_ROOT_PASSWORD: 12345678
       MYSQL_DATABASE: ps
       MYSQL_USER: ps
       MYSQL_PASSWORD: ps
    ports:
      - "3306:3306"
    networks:
      - my_net
  ps:
    container_name: ps-web
    volumes:
      - "$PWD/html:/var/www/html"
    depends_on:
      - db
    image: prestashop/prestashop
    ports:
      - "80:80"
    environment:
      DB_SERVER: db
      DB_USER: ps
      DB_PASSWD: ps
      DB_NAME: ps
    networks:
      - my_net
networks:
  my_net:

## joomla + mysql ========
version: '3'
services:
  web:
    volumes:
      - $PWD/html:/var/www/html
    image: joomla
    ports:
      - 80:80
    environment:
      JOOMLA_DB_HOST: db
      JOOMLA_DB_PASSWORD: joomla
      JOOMLA_DB_USER: joomla
      JOOMLA_DB_NAME: joomla
  db:
    image: mysql:5.7
    volumes:
      - $PWD/data:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_USER: joomla
      MYSQL_DATABASE: joomla
      MYSQL_PASSWORD: joomla

# ===== subir imagenes=====
mkdir registry
cd registry/
docker run -d -p 5000:5000 --name registry -v $PWD/data:/var/lib/registry registry:2
docker pull hello-world
docker tag hello-world:latest localhost:5000/hello-world
docker push localhost:5000/hello-world
docker rmi hello-world localhost:5000/hello-world   <--eliminar estas imagenes
docker pull localhost:5000/hello-world

## compartir en la red ======
saber nuestra ip con  "ip a" y ponemos la ip en vez de localhost
