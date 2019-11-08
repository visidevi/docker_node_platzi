# Curso de Docker en Platzi

Ésta es una aplicación de ejemplo para el curso de Docker de Platzi por Guido
Vilariño.

Encuentra más información en https://platzi.com, suscríbete al curso y aprende
a usar Docker de manera profesional.


### NETWORKING DOCKER: Colaboración entre contenedores
``` 
docker network ls
```
    crear una red agregamos el --flag attachable: Permite que contenedores en el futuro se conecten a esta red.
```
docker network create --attachable <name>
```
Creamos los contenedores de base de datos
```
docker run -d --name db mongo
```
conectamos el contenedor a la red
```
docker network connect <redname> db
```
Miramos que contenedores tenemos en nuestra red
```
docker network inspect <redname>
```
Corremos contenedor de la aplicación
```
docker run  -d --name app - p 3000:3000 --env MONGO_URL=mongodb://db:27017/test <imagename>
```
--flag: --env : variable de entorno que recibe el index.js
si dos contenedores se están en el mismo networking pueden verse entre si utilizando como hostname el nombre del contenedor como
"la dirección de la computadora"


### DOCKER COMPOSE : TODO EN UNO
nos permite describir de forma declarativa la arquitectura de nuestra aplicación,usando el composefile (docker-compose.yml)
Borrarmos todos nuestros contendores
```
docker rm -f $(docker ps -aq)
```
```
docker network rm <redname>
```
*La sintaxis yml* es muy usada en el mundo de python,
para describir como queremos que sea nuestra app. esto básicamente indica que debería soportar docker compose para construir nuestra app.


```
version: "3"

services:
  app:
    image: platziapp
    environment:
      MONGO_URL: "mongodb://db:27017/test"
    depends_on:
      - db
    ports:
      - "3000:3000"

  db:
    image: mongo

```
- **services**: componentes que sirven a la totalidad de la aplicación
los servicios pueden tener mas de un contenedor
en el ejemplo tenemos dos servicios:
app y db
- **image**: Los contenedores de este servicios van a ser iniciados a partir de la imagen mongo
- **enviroment**: Variables de entorno
- **depends_on**: Este servicio depende de otros servicios, quiere decir que. Inicia el servicio solo cuando el servicio del el cual depende esta inicializado.
garantiza que el componente esta creado pero no que esta corriendo, no es para controlar el orden en el cual inician los contenedores.

```
docker-compose up
```
ya no tenemos que crear una network docker-compose la crea y hace la conexión el mismo.
```
docker-compose up -d
```
corre el compose file pero no muestra el output
```
docker-compose ps
```
docker compose facilita el uso de docker, por defecto toma el nombre del directorio donde estamos parados y lo mismo pasa con el network
```
    Name                  Command               State           Ports
------------------------------------------------------------------------------
docker_app_1   docker-entrypoint.sh npx n ...   Up      0.0.0.0:3000->3000/tcp
docker_db_1    docker-entrypoint.sh mongod      Up      27017/tcp

NETWORK ID          NAME                DRIVER              SCOPE
866bfe2347e0        bridge              bridge              local
9603e0abcecb        docker_default      bridge              local
```
```
docker-compose logs app
```
Ver los logs del servicio especifico
```
docker-compose exec app bash
```
ingresar al contenedor de la aplicación , nos ahorramos el comando -it
```
ls -lac
```
```
total 144
drwxr-xr-x   1 root root   4096 Nov  8 14:41 .
drwxr-xr-x   1 root root   4096 Nov  8 14:41 ..
-rw-r--r--   1 root root   1072 Nov  8 14:41 LICENSE
drwxr-xr-x   2 root root   4096 Nov  8 14:41 build
-rw-r--r--   1 root root    194 Nov  8 14:41 docker-compose.yml
-rw-r--r--   1 root root    561 Nov  8 14:41 index.js
drwxr-xr-x 242 root root  12288 Nov  8 14:41 node_modules
-rw-r--r--   1 root root 101113 Nov  8 14:41 package-lock.json
-rw-r--r--   1 root root    663 Nov  8 14:41 package.json
drwxr-xr-x   2 root root   4096 Nov  8 14:41 test
```
**Eliminamos todos los servicios, todos los contenedores todo**
```
docker-compose down
```
```
Stopping docker_app_1 ... done
Stopping docker_db_1  ... done
Removing docker_app_1 ... done
Removing docker_db_1  ... done
```
