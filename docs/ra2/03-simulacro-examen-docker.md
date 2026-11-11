# Simulacro de examen de docker

Escribe las órdenes de Docker necesarias para resolver cada apartado. No es necesario entregar la ejecución de las órdenes. Los ejercicios están basados en los contenidos de las [sesiones 1](https://iesgn.github.io/curso_docker_2021/sesion1/), [2](https://iesgn.github.io/curso_docker_2021/sesion2/), [3](https://iesgn.github.io/curso_docker_2021/sesion3/) y [4](https://iesgn.github.io/curso_docker_2021/sesion4/) del curso.

!!! warning "Descargar iso de alpine linux con docker"
    Puedes descargar esta *ova* de virtualbox con *alpine linux* que tiene ya instalado docker.
    Solo hay un usuario que se llama *root* y sin contraseña.
    Debería tener abiert

## Ejercicios para hacer *a mano*

Estos ejercicios son para hacerlos *a mano*. Puede que tengas que revisar alguna cosa en la documentación.

### Ejercicio 1

Escribe la orden para ejecutar un contenedor docker con estas características:

1. Que se ejecute en segundo plano.
2. Que el nombre del contenedor sea *grafana*.
3. Que exponga el puerto 3000 del contenedor en el puerto 3000 del sistema operativo que ejecuta docker.
4. Que se ejecute el contenedor llamado *grafana/grafana*.

```bash

```

---

### Ejercicio 2. Contenedor de prueba

1. Crea y ejecuta un contenedor llamado `prueba` a partir de la imagen `hello-world`.
2. Comprueba que el contenedor ha terminado y consulta los contenedores parados.
3. Elimina el contenedor `prueba`.

```bash

```

---

### Ejercicio 3. Servidor web e imágenes

Escribe las órdenes para descargar la imagen `nginx:stable`, comprobar que está disponible localmente y crear un contenedor llamado `web` que:

1. Se ejecute en segundo plano.
2. Publique el puerto 80 del contenedor en el puerto 8181 del anfitrión.
3. Permita consultar sus registros y, al terminar, detener y eliminar el contenedor.

```bash

```

---

### Ejercicio 4. Persistencia con un volumen

Supón que tienes un fichero `index.html` en el directorio actual. Escribe las órdenes para:

1. Crear un volumen llamado `web_data`.
2. Crear un contenedor llamado `web1` con la imagen `php:7.4-apache`, publicando el puerto 80 del contenedor en el 8080 del anfitrión y montando `web_data` en `/var/www/html`.
3. Copiar `index.html` al directorio web del contenedor.
4. Eliminar `web1` y crear otro contenedor llamado `web2` con la misma imagen, el mismo puerto y el mismo volumen. Indica también cómo comprobar que el fichero sigue disponible.

```bash

```

---

### Ejercicio 5. Compartir un directorio con bind mount

Supón que existe en el anfitrión el directorio `/home/alumno/pagina` y que contiene un fichero `index.html`. Escribe las órdenes para crear dos contenedores a partir de `php:7.4-apache`, llamados `web1` y `web2`, que monten ese directorio en `/var/www/html`. El primero debe publicarse en el puerto 8181 del anfitrión y el segundo en el 8282.

```bash

```

---

### Ejercicio 6. Aplicación conectada a Redis

La aplicación `iesgn/guestbook` utiliza el nombre `redis` para conectarse a una base de datos Redis. Escribe las órdenes para:

1. Crear una red bridge llamada `red_guestbook`.
2. Ejecutar un contenedor Redis llamado `redis` conectado a esa red. No publiques sus puertos en el anfitrión.
3. Ejecutar la aplicación en un contenedor llamado `guestbook`, conectado a la misma red y publicando el puerto 5000 del contenedor en el 8080 del anfitrión.
4. Consultar la configuración de la red para comprobar que ambos contenedores están conectados.

```bash

```

---

### Ejercicio 7. Compartir datos con un volumen

Escribe las órdenes para comprobar que un volumen permite conservar datos aunque se elimine un contenedor:

1. Crea un volumen llamado `datos_compartidos`.
2. Ejecuta un contenedor llamado `escritor` con la imagen `alpine`, montando el volumen en `/datos`. Dentro del contenedor, crea el fichero `/datos/mensaje.txt` con el texto `Hola, Docker`.
3. Elimina el contenedor `escritor`.
4. Ejecuta un segundo contenedor llamado `lector`, montando el mismo volumen en `/datos`, y muestra el contenido de `/datos/mensaje.txt`.
5. Elimina el contenedor `lector` y el volumen.

```bash
```

---

### Ejercicio 8. Contenedor interactivo

Escribe las órdenes para crear un contenedor interactivo llamado `debian_prueba` a partir de `debian:bookworm`, instalar `nano`, salir del contenedor y volver a iniciarlo de forma interactiva. Finalmente, elimina el contenedor.

```bash

```

---

### Ejercicio 9. Comunicación entre contenedores en una red

Escribe las órdenes para:

1. Crear una red llamada `red_prueba`.
2. Ejecutar dos contenedores llamados `contenedor1` y `contenedor2` a partir de la imagen `busybox`, conectados a esa red y dejándolos en ejecución.
3. Comprobar desde `contenedor1` que puede comunicarse con `contenedor2` usando su nombre.
4. Eliminar los contenedores y la red.

```bash

```

---

### Ejercicio 10. Aplicación Nextcloud con MariaDB

Escribe las órdenes para desplegar Nextcloud y MariaDB en una red bridge llamada `red_nextcloud`:

1. Crea un volumen `nextcloud_db_data` para los datos de MariaDB y otro `nextcloud_data` para los datos de Nextcloud.
2. Configura MariaDB para crear la base de datos `nextcloud`, el usuario `nc_user` y la contraseña `nc_pass`. Establece la contraseña de root como `root`.
3. Ejecuta Nextcloud conectado a la misma red y configurado para utilizar esa base de datos.
4. Publica el puerto 80 de Nextcloud en el puerto 8080 del anfitrión.

```bash

```

---

## Ejercicios para hacer con el ordenador


### Ejercicio 11. Instalar *mariadb/joomla*

La idea es crear una aplicación con dos contenedores.

1. Un contenedor con la base de datos *mariadb*.
2. El otro contenedor es el gestor de contenidos *joomla*.


### Contenedor *mariadb*

El contenedor mariadb lo tienes que ejecutar de la siguiente manera. Presta especial atención a la configuración de variables de entorno para inicializar la base de datos.

```bash
docker run -d \
  # Parámetros de docker \
  mariadb:11
```

---

### Contenedor *joomla*

El contenedor joomla lo tienes que ejecutar de la siguiente manera. Presta especial atención a la configuración de variables de entorno para enlazar automáticamente el CMS con la base de datos.

```bash
docker run -d \
  # Parámetros de docker \
  joomla:latest
```

### Qué tengo que hacer

En primer lugar, ambos contenedores tienen que estar en la misma red.

**Contenedor *mariadb***

Tienes que ejecutar este contenedor incluyendo las siguientes opciones, que son parámetros de la orden `docker run`.

1. El nombre del contenedor tiene que ser *mariadb*.
2. El contenedor tiene que tener un volumen llamado `joomla_db_data` asociado al directorio `/var/lib/mysql` del contenedor.
3. Añade el parámetro `--restart=always` para que el contenedor se inicie cuando arranque docker.
4. Añade la variable de entorno `MARIADB_ROOT_PASSWORD` con el valor `root`.
5. Añade la variable de entorno `MARIADB_DATABASE` con el valor `joomla_db`.
6. Añade la variable de entorno `MARIADB_USER` con el valor `usuario`.
7. Añade la variable de entorno `MARIADB_PASSWORD` con el valor `usuario`.

**Contenedor *joomla***

Tienes que ejecutar este contenedor incluyendo las siguientes opciones, que son parámetros de la orden `docker run`.

1. El nombre del contenedor tiene que ser *joomla*.
2. El puerto 80 del contenedor tiene que exponerse al sistema operativo que ejecute docker en el puerto 8080.
3. El contenedor tiene que tener un volumen llamado `joomla_site_data` asociado al directorio `/var/www/html` del contenedor.
4. Añade el parámetro `--restart=always` para que el contenedor se inicie cuando arranque docker.
5. Añade la variable de entorno `JOOMLA_DB_HOST` con el valor `mariadb` para conectar este contenedor con la base de datos.
6. Añade la variable de entorno `JOOMLA_DB_USER` con el valor `usuario`.
7. Añade la variable de entorno `JOOMLA_DB_PASSWORD` con el valor `usuario`.
8. Añade la variable de entorno `JOOMLA_DB_NAME` con el valor `joomla_db`.


### Ejercicio 12. Instalar *mariadb/drupal*

La idea es crear una aplicación con dos contenedores.

1. Un contenedor con la base de datos *mariadb*.
2. El otro contenedor es el gestor de contenidos *drupal*.


### Contenedor *mariadb*

El contenedor mariadb lo tienes que ejecutar de la siguiente manera. Presta especial atención a la configuración de variables de entorno para inicializar la base de datos.

```bash
docker run -d \
  # Parámetros de docker \
  mariadb:11
```

### Contenedor *drupal*

El contenedor drupal lo tienes que ejecutar de la siguiente manera. Presta especial atención a la exposición de puertos y la gestión de la persistencia mediante volúmenes.

```bash
docker run -d \
  # Parámetros de docker \
  drupal:latest
```

### Qué tengo que hacer

En primer lugar, ambos contenedores tienen que estar en la misma red.

**Contenedor *mariadb***

Tienes que ejecutar este contenedor incluyendo las siguientes opciones, que son parámetros de la orden `docker run`.

1. El nombre del contenedor tiene que ser *mariadb*.
2. El contenedor tiene que tener un volumen llamado `drupal_db_data` asociado al directorio `/var/lib/mysql` del contenedor.
3. Añade el parámetro `--restart=always` para que el contenedor se inicie cuando arranque docker.
4. Añade la variable de entorno `MARIADB_ROOT_PASSWORD` con el valor `root`.
5. Añade la variable de entorno `MARIADB_DATABASE` con el valor `drupal_db`.
6. Añade la variable de entorno `MARIADB_USER` con el valor `usuario`.
7. Añade la variable de entorno `MARIADB_PASSWORD` con el valor `usuario`.

**Contenedor *drupal***

Tienes que ejecutar este contenedor incluyendo las siguientes opciones, que son parámetros de la orden `docker run`.

1. El nombre del contenedor tiene que ser *drupal*.
2. El puerto 80 del contenedor tiene que exponerse al sistema operativo que ejecute docker en el puerto 8080.
3. El contenedor tiene que tener un volumen llamado `drupal_site_data` asociado al directorio `/var/www/html` del contenedor.
4. Añade el parámetro `--restart=always` para que el contenedor se inicie cuando arranque docker.
5. Añade la variable de entorno `DRUPAL_DB_HOST` con el valor `mariadb` para conectar este contenedor con la base de datos.
6. Añade la variable de entorno `DRUPAL_DB_USER` con el valor `usuario`.
7. Añade la variable de entorno `DRUPAL_DB_PASSWORD` con el valor `usuario`.
8. Añade la variable de entorno `DRUPAL_DB_NAME` con el valor `drupal_db`.

---

### Ejercicio 13. Instalar una aplicación *redis/python*

La idea es crear una aplicación con dos contenedores.

1. Un contenedor con la base de datos *redis*
2. El otro contenedor es un servidor de código *python*.


### Contenedor *redis*

El contenedor redis lo tienes que ejecutar de la siguiente manera. Presta especial atención a la línea 3, pues en este caso hay que ejecutar el contenedor con esos argumentos.

```bash hl_lines="3" linenums="1"
docker run \
  # Parámetros de docker \
  redis:alpine redis-server --appendonly yes
```

### Contenedor *python*

El contenedor python lo tienes que ejecutar de la siguiente manera. Presta especial atención a la línea 3, pues en este caso hay que ejecutar el contenedor con esos argumentos.

```bash hl_lines="3" linenums="1"
docker run -d \
  # Parámetros de docker \
  python:3.11-alpine sh -c "pip install flask redis && python app.py"
```

### Qué tengo que hacer

En primer lugar, ambos contenedores tienen que estar en la misma red.

**Contenedor *redis***

Tienes que ejecutar este contenedor incluyendo las siguienes opciones, que son parámetros de la orden `docker run`.

1. El nombre del contenedor tiene que ser *redis*.
2. El contenedor tiene que tener un volumen llamado `redis_data` asociado al directorio `/data` del contenedor.
3. Añade el parámetro `--restart=always` para que el contenedor se inice cuando arranque docker.

**Contenedor *python***

Tienes que ejecutar este contenedor incluyendo las siguienes opciones, que son parámetros de la orden `docker run`.

1. El nombre del contenedor tiene que ser *backend*.
2. El puerto 5000 tiene que exponerse al sistema operativo que ejecute docker también en el puerto 5000.
3. El contenedor tiene que tener un bind mount a la carpeta `app` que se proporciona. Este bind mount está asociado al directorio `/app` del contenedor.
4. Añade el parámetro `--restart=always` para que el contenedor se inice cuando arranque docker.
5. Añade el parámetro `-w /app` para indicar en qué directorio está el ejecutable de python.
6. Añade el parámetro `-e REDIS_HOST=redis` para conectar este contenedor con el otro.

Crea en el sistema operativo anfitrión una carpeta que se llame `app` y dentro crea un fichero que se llame `app.py` con este contenido:

```python
import os
import time
from flask import Flask
import redis

app = Flask(__name__)

# Lee el host de Redis desde la variable de entorno REDIS_HOST (por defecto 'redis')
redis_host = os.environ.get('REDIS_HOST', 'redis')
cache = redis.Redis(host=redis_host, port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            # Incrementa atómicamente el valor en Redis
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def index():
    count = get_hit_count()
    return f'''
    <!DOCTYPE html>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <title>Simulacro IAW - Docker</title>
    </head>
    <body style="font-family: Arial, sans-serif; text-align: center; margin-top: 50px;">
        <h1>Simulacro Examen IAW</h1>
        <h2>Visitas registradas en Redis: <b style="color: #007bff; font-size: 2em;">{count}</b></h2>
        <p>Recarga la página (F5) para comprobar que el contador incrementa.</p>
    </body>
    </html>
    '''

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

## Ejercicios de docker-compose a órdenes docker

### Ejercicio 14. *phpmyadmin/mysql*

Convierte este fichero `docker-compose.yml` en órdenes:

```yml
version: '3.8'

services:
  db:
    image: mysql:8.0
    container_name: servidor_mysql
    environment:
      MYSQL_ROOT_PASSWORD: clave_super_segura
      MYSQL_DATABASE: mi_base_datos
    networks:
      - red_bd

  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    container_name: cliente_pma
    ports:
      - "8080:80"
    environment:
      PMA_HOST: servidor_mysql
    networks:
      - red_bd
    depends_on:
      - db

networks:
  red_bd:
    driver: bridge
```

### Ejercicio 15. *wordpress/mysql*

```yml
version: '3.8'

services:
  db_wp:
    image: mysql:5.7
    container_name: contenedor_mysql_wp
    environment:
      MYSQL_ROOT_PASSWORD: secret_root_pass
      MYSQL_DATABASE: wp_db
      MYSQL_USER: wp_user
      MYSQL_PASSWORD: wp_password
    networks:
      - red_wordpress

  web_wp:
    image: wordpress:latest
    container_name: contenedor_wordpress
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: contenedor_mysql_wp:3306
      WORDPRESS_DB_USER: wp_user
      WORDPRESS_DB_PASSWORD: wp_password
      WORDPRESS_DB_NAME: wp_db
    networks:
      - red_wordpress
    depends_on:
      - db_wp

networks:
  red_wordpress:
    driver: bridge
```

### Ejercicio 16. *nextcloud/postgresql*

```yml
version: '3.8'

services:
  db_nc:
    image: postgres:15-alpine
    container_name: postgres_nextcloud
    environment:
      POSTGRES_DB: nextcloud_db
      POSTGRES_USER: nc_user
      POSTGRES_PASSWORD: nc_password
    networks:
      - red_cloud

  app_nc:
    image: nextcloud:latest
    container_name: servidor_nextcloud
    ports:
      - "8080:80"
    environment:
      POSTGRES_HOST: postgres_nextcloud
      POSTGRES_DB: nextcloud_db
      POSTGRES_USER: nc_user
      POSTGRES_PASSWORD: nc_password
      NEXTCLOUD_ADMIN_USER: admin
      NEXTCLOUD_ADMIN_PASSWORD: admin_password
    networks:
      - red_cloud
    depends_on:
      - db_nc

networks:
  red_cloud:
    driver: bridge
```

### Ejercicio 17. *mediawiki/mariadb*

```yml
version: '3.8'

services:
  db_wiki:
    image: mariadb:10.11
    container_name: mariadb_wiki
    environment:
      MARIADB_ROOT_PASSWORD: root_secret_pass
      MARIADB_DATABASE: wiki_db
      MARIADB_USER: wiki_user
      MARIADB_PASSWORD: wiki_password
    networks:
      - red_wiki

  web_wiki:
    image: mediawiki:latest
    container_name: servidor_mediawiki
    ports:
      - "80:80"
    environment:
      MEDIAWIKI_DB_HOST: mariadb_wiki
      MEDIAWIKI_DB_USER: wiki_user
      MEDIAWIKI_DB_PASSWORD: wiki_password
      MEDIAWIKI_DB_NAME: wiki_db
    networks:
      - red_wiki
    depends_on:
      - db_wiki

networks:
  red_wiki:
    driver: bridge
```

### Ejercicio 18. *gitea/mysql*

```yml
version: '3.8'

services:
  db_git:
    image: mysql:8.0
    container_name: mysql_gitea
    environment:
      MYSQL_ROOT_PASSWORD: root_pass_gitea
      MYSQL_DATABASE: gitea_db
      MYSQL_USER: gitea_user
      MYSQL_PASSWORD: gitea_password
    networks:
      - red_gitea

  app_git:
    image: gitea/gitea:latest
    container_name: servidor_gitea
    ports:
      - "3000:3000"
      - "2222:22"
    environment:
      GITEA__database__DB_TYPE: mysql
      GITEA__database__HOST: mysql_gitea:3306
      GITEA__database__NAME: gitea_db
      GITEA__database__USER: gitea_user
      GITEA__database__PASSWD: gitea_password
    networks:
      - red_gitea
    depends_on:
      - db_git

networks:
  red_gitea:
    driver: bridge
```

---

## Soluciones

Las órdenes de esta sección son una posible solución. Los despliegues de Guestbook, Nextcloud, Joomla, Drupal y Redis/Python son independientes: algunos usan nombres de contenedor repetidos y no se deben crear a la vez sin cambiar esos nombres.

### Solución del ejercicio 1

```bash
docker run -d --name grafana -p 3000:3000 grafana/grafana
```

### Solución del ejercicio 2

```bash
docker run --name prueba hello-world
docker ps -a
docker rm prueba
```

### Solución del ejercicio 3

```bash
docker pull nginx:stable
docker images nginx
docker run -d --name web -p 8181:80 nginx:stable
docker logs web
docker stop web
docker rm web
```

### Solución del ejercicio 4

```bash
docker volume create web_data
docker run -d --name web1 -p 8080:80 --mount source=web_data,target=/var/www/html php:7.4-apache
docker cp index.html web1:/var/www/html/index.html
docker stop web1
docker rm web1
docker run -d --name web2 -p 8080:80 --mount source=web_data,target=/var/www/html php:7.4-apache
docker exec web2 ls -l /var/www/html/index.html
```

### Solución del ejercicio 5

```bash
docker run -d --name web1 -p 8181:80 --mount type=bind,source=/home/alumno/pagina,target=/var/www/html php:7.4-apache
docker run -d --name web2 -p 8282:80 --mount type=bind,source=/home/alumno/pagina,target=/var/www/html php:7.4-apache
```

### Solución del ejercicio 6

```bash
docker network create red_guestbook
docker run -d --name redis --network red_guestbook redis
docker run -d --name guestbook --network red_guestbook -p 8080:5000 iesgn/guestbook
docker network inspect red_guestbook
```

### Solución del ejercicio 7

```bash
docker volume create datos_compartidos
docker run --name escritor --mount source=datos_compartidos,target=/datos alpine sh -c "echo 'Hola, Docker' > /datos/mensaje.txt"
docker rm escritor
docker run --name lector --mount source=datos_compartidos,target=/datos alpine cat /datos/mensaje.txt
docker rm lector
docker volume rm datos_compartidos
```

### Solución del ejercicio 8

En la primera sesión interactiva, ejecuta:

```bash
docker run -it --name debian_prueba debian:bookworm bash
apt-get update
apt-get install -y nano
exit
```

Al volver al terminal del anfitrión, reinicia el contenedor y entra de nuevo:

```bash
docker start -ai debian_prueba
```

Después de salir otra vez del contenedor:

```bash
docker rm debian_prueba
```

### Solución del ejercicio 9

```bash
docker network create red_prueba
docker run -dit --name contenedor1 --network red_prueba busybox sleep 3600
docker run -dit --name contenedor2 --network red_prueba busybox sleep 3600
docker exec contenedor1 ping -c 3 contenedor2
docker rm -f contenedor1 contenedor2
docker network rm red_prueba
```

### Solución del ejercicio 10

```bash
docker network create red_nextcloud
docker volume create nextcloud_db_data
docker volume create nextcloud_data
docker run -d --name mariadb --network red_nextcloud --mount source=nextcloud_db_data,target=/var/lib/mysql -e MARIADB_ROOT_PASSWORD=root -e MARIADB_DATABASE=nextcloud -e MARIADB_USER=nc_user -e MARIADB_PASSWORD=nc_pass mariadb:10.5
docker run -d --name nextcloud --network red_nextcloud -p 8080:80 --mount source=nextcloud_data,target=/var/www/html -e MYSQL_HOST=mariadb -e MYSQL_DATABASE=nextcloud -e MYSQL_USER=nc_user -e MYSQL_PASSWORD=nc_pass nextcloud
```

### Solución del ejercicio 11 Joomla y MariaDB

```bash
docker network create joomla_net
docker run -d --name mariadb --network joomla_net --mount source=joomla_db_data,target=/var/lib/mysql --restart=always -e MARIADB_ROOT_PASSWORD=root -e MARIADB_DATABASE=joomla_db -e MARIADB_USER=usuario -e MARIADB_PASSWORD=usuario mariadb:11
docker run -d --name joomla --network joomla_net -p 8080:80 --mount source=joomla_site_data,target=/var/www/html --restart=always -e JOOMLA_DB_HOST=mariadb -e JOOMLA_DB_USER=usuario -e JOOMLA_DB_PASSWORD=usuario -e JOOMLA_DB_NAME=joomla_db joomla:latest
```

### Solución del ejercicio 12 Drupal y MariaDB

```bash
docker network create drupal_net
docker run -d --name mariadb --network drupal_net --mount source=drupal_db_data,target=/var/lib/mysql --restart=always -e MARIADB_ROOT_PASSWORD=root -e MARIADB_DATABASE=drupal_db -e MARIADB_USER=usuario -e MARIADB_PASSWORD=usuario mariadb:11
docker run -d --name drupal --network drupal_net -p 8080:80 --mount source=drupal_site_data,target=/var/www/html --restart=always -e DRUPAL_DB_HOST=mariadb -e DRUPAL_DB_USER=usuario -e DRUPAL_DB_PASSWORD=usuario -e DRUPAL_DB_NAME=drupal_db drupal:latest
```

Estas opciones de entorno reproducen lo pedido en el enunciado. La imagen oficial `drupal` puede requerir configurar la conexión a la base de datos durante el asistente de instalación web; no debe asumirse que esas variables bastan para completar la instalación automáticamente.

### Solución del ejercicio 13 Redis y Python

Guarda en `app/app.py` el código Python proporcionado en el enunciado y ejecuta:

```bash
docker network create python_net
docker run -d --name redis --network python_net --mount source=redis_data,target=/data --restart=always redis:alpine redis-server --appendonly yes
docker run -d --name backend --network python_net -p 5000:5000 --mount type=bind,source="$(pwd)/app",target=/app --restart=always -w /app -e REDIS_HOST=redis python:3.11-alpine sh -c "pip install flask redis && python app.py"
```

### Solución del ejercicio 14

```bash
# Crear la red personalizada
docker network create red_bd;

# Desplegar el contenedor de MySQL
docker run -d \
  --name servidor_mysql \
  --network red_bd \
  -e MYSQL_ROOT_PASSWORD=clave_super_segura \
  -e MYSQL_DATABASE=mi_base_datos \
  mysql:8.0;

# Desplegar el contenedor de phpMyAdmin
docker run -d \
  --name cliente_pma \
  --network red_bd \
  -p 8080:80 \
  -e PMA_HOST=servidor_mysql \
  phpmyadmin/phpmyadmin:latest;
```

Conceptos clave de este ejercicio

1. **Creación previa de redes (`docker network create`)**: Identificar que Docker Compose crea las redes implícitamente, pero en la CLI hay que crearlas antes de usarlas.
2. **Conexión entre contenedores (`PMA_HOST`)**: Entender que al estar en la misma red, phpMyAdmin encuentra a MySQL usando el valor de `--name` (`servidor_mysql`) como nombre de host DNS.
3. **Paso de parámetros CLI**: Traducir correctamente las claves del YAML a sus banderas CLI:
    - `environment`: --> `-e / --env`
    - `ports`: --> `-p / --publish`
    - `container_name`: --> `--name`
    - `networks`: --> `--network`

4. **Orden de ejecución (`depends_on`)**: Notar que primero hay que arrancar la base de datos y luego el cliente web.

### Solución del ejercicio 15

```bash
# Crear la red dedicada
docker network create red_wordpress;

# Lanzar el contenedor de MySQL
docker run -d \
  --name contenedor_wordpress \
  --network red_wordpress \
  -p 80:80 \
  -e WORDPRESS_DB_HOST=contenedor_mysql_wp:3306 \
  -e WORDPRESS_DB_USER=wp_user \
  -e WORDPRESS_DB_PASSWORD=wp_password \
  -e WORDPRESS_DB_NAME=wp_db \
  wordpress:latest;
```

### Solución del ejercicio 16

```bash
# Crear la red
docker network create red_cloud;

# Desplegar PostgreSQL
docker run -d \
  --name postgres_nextcloud \
  --network red_cloud \
  -e POSTGRES_DB=nextcloud_db \
  -e POSTGRES_USER=nc_user \
  -e POSTGRES_PASSWORD=nc_password \
  postgres:15-alpine;

# Desplegar Nextcloud
docker run -d \
  --name servidor_nextcloud \
  --network red_cloud \
  -p 8080:80 \
  -e POSTGRES_HOST=postgres_nextcloud \
  -e POSTGRES_DB=nextcloud_db \
  -e POSTGRES_USER=nc_user \
  -e POSTGRES_PASSWORD=nc_password \
  -e NEXTCLOUD_ADMIN_USER=admin \
  -e NEXTCLOUD_ADMIN_PASSWORD=admin_password \
  nextcloud:latest;

```

### Solución del ejercicio 17

```bash
# Crear la red
docker network create red_wiki;

# Desplegar MariaDB
docker run -d \
  --name mariadb_wiki \
  --network red_wiki \
  -e MARIADB_ROOT_PASSWORD=root_secret_pass \
  -e MARIADB_DATABASE=wiki_db \
  -e MARIADB_USER=wiki_user \
  -e MARIADB_PASSWORD=wiki_password \
  mariadb:10.11;

# Desplegar mediawiki
docker run -d \
  --name servidor_mediawiki \
  --network red_wiki \
  -p 80:80 \
  -e MEDIAWIKI_DB_HOST=mariadb_wiki \
  -e MEDIAWIKI_DB_USER=wiki_user \
  -e MEDIAWIKI_DB_PASSWORD=wiki_password \
  -e MEDIAWIKI_DB_NAME=wiki_db \
  mediawiki:latest;
```

### Solución del ejercicio 18

```bash
# Crear la red
docker network create red_gitea;

# Desplegar mysql
docker run -d \
  --name mysql_gitea \
  --network red_gitea \
  -e MYSQL_ROOT_PASSWORD=root_pass_gitea \
  -e MYSQL_DATABASE=gitea_db \
  -e MYSQL_USER=gitea_user \
  -e MYSQL_PASSWORD=gitea_password \
  mysql:8.0;

# Desplegar Gitea con dos puertos expuestos
docker run -d \
  --name servidor_gitea \
  --network red_gitea \
  -p 3000:3000 \
  -p 2222:22 \
  -e GITEA__database__DB_TYPE=mysql \
  -e GITEA__database__HOST=mysql_gitea:3306 \
  -e GITEA__database__NAME=gitea_db \
  -e GITEA__database__USER=gitea_user \
  -e GITEA__database__PASSWD=gitea_password \
  gitea/gitea:latest;
```