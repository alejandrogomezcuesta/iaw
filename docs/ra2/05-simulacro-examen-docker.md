# Simulacro de examen de docker

Escribe las órdenes de Docker necesarias para resolver cada apartado. No es necesario entregar la ejecución de las órdenes. Los ejercicios están basados en los contenidos de las [sesiones 1](https://iesgn.github.io/curso_docker_2021/sesion1/), [2](https://iesgn.github.io/curso_docker_2021/sesion2/), [3](https://iesgn.github.io/curso_docker_2021/sesion3/) y [4](https://iesgn.github.io/curso_docker_2021/sesion4/) del curso.


## Ejercicio 1

Escribe la orden para ejecutar un contenedor docker con estas características:

1. Que se ejecute en segundo plano.
2. Que el nombre del contenedor sea *grafana*.
3. Que exponga el puerto 3000 del contenedor en el puerto 3000 del sistema operativo que ejecuta docker.
4. Que se ejecute el contenedor llamado *grafana/grafana*.

```bash

```

---

## Ejercicio 2. Contenedor de prueba

1. Crea y ejecuta un contenedor llamado `prueba` a partir de la imagen `hello-world`.
2. Comprueba que el contenedor ha terminado y consulta los contenedores parados.
3. Elimina el contenedor `prueba`.

```bash

```

---

## Ejercicio 3. Servidor web e imágenes

Escribe las órdenes para descargar la imagen `nginx:stable`, comprobar que está disponible localmente y crear un contenedor llamado `web` que:

1. Se ejecute en segundo plano.
2. Publique el puerto 80 del contenedor en el puerto 8181 del anfitrión.
3. Permita consultar sus registros y, al terminar, detener y eliminar el contenedor.

```bash

```

---

## Ejercicio 4. Persistencia con un volumen

Supón que tienes un fichero `index.html` en el directorio actual. Escribe las órdenes para:

1. Crear un volumen llamado `web_data`.
2. Crear un contenedor llamado `web1` con la imagen `php:7.4-apache`, publicando el puerto 80 del contenedor en el 8080 del anfitrión y montando `web_data` en `/var/www/html`.
3. Copiar `index.html` al directorio web del contenedor.
4. Eliminar `web1` y crear otro contenedor llamado `web2` con la misma imagen, el mismo puerto y el mismo volumen. Indica también cómo comprobar que el fichero sigue disponible.

```bash

```

---

## Ejercicio 5. Compartir un directorio con bind mount

Supón que existe en el anfitrión el directorio `/home/alumno/pagina` y que contiene un fichero `index.html`. Escribe las órdenes para crear dos contenedores a partir de `php:7.4-apache`, llamados `web1` y `web2`, que monten ese directorio en `/var/www/html`. El primero debe publicarse en el puerto 8181 del anfitrión y el segundo en el 8282.

```bash

```

---

## Ejercicio 6. Aplicación conectada a Redis

La aplicación `iesgn/guestbook` utiliza el nombre `redis` para conectarse a una base de datos Redis. Escribe las órdenes para:

1. Crear una red bridge llamada `red_guestbook`.
2. Ejecutar un contenedor Redis llamado `redis` conectado a esa red. No publiques sus puertos en el anfitrión.
3. Ejecutar la aplicación en un contenedor llamado `guestbook`, conectado a la misma red y publicando el puerto 5000 del contenedor en el 8080 del anfitrión.
4. Consultar la configuración de la red para comprobar que ambos contenedores están conectados.

```bash

```

---

## Ejercicio 7. Buscar, descargar e inspeccionar imágenes

Escribe las órdenes para buscar imágenes de Apache HTTP Server en Docker Hub, descargar `httpd:2.4`, comprobar que se ha descargado e inspeccionar la imagen. Después crea una etiqueta local llamada `mi-httpd` para esa misma imagen.

```bash

```

---

## Ejercicio 8. Contenedor interactivo

Escribe las órdenes para crear un contenedor interactivo llamado `debian_prueba` a partir de `debian:bookworm`, instalar `nano`, salir del contenedor y volver a iniciarlo de forma interactiva. Finalmente, elimina el contenedor.

```bash

```

---

## Ejercicio 9. Redes bridge y comunicación entre contenedores

Escribe las órdenes para crear dos redes bridge:

1. `red1`, con la red `172.28.0.0/16` y la puerta de enlace `172.28.0.1`.
2. `red2`, con la configuración de red asignada automáticamente por Docker.

Después, ejecuta dos contenedores Ubuntu llamados `u1` y `u2`, con nombres de host `host1` y `host2`, conectados respectivamente a `red1` y `red2`. Asigna a `u1` la IP `172.28.0.10`. Instala `inetutils-ping` en ambos contenedores, comprueba que inicialmente no se comunican y conecta `u1` también a `red2` para que puedan comunicarse por nombre.

```bash

```

---

## Ejercicio 10. Aplicación Nextcloud con MariaDB

Escribe las órdenes para desplegar Nextcloud y MariaDB en una red bridge llamada `red_nextcloud`:

1. Crea un volumen `nextcloud_db_data` para los datos de MariaDB y otro `nextcloud_data` para los datos de Nextcloud.
2. Configura MariaDB para crear la base de datos `nextcloud`, el usuario `nc_user` y la contraseña `nc_pass`. Establece la contraseña de root como `root`.
3. Ejecuta Nextcloud conectado a la misma red y configurado para utilizar esa base de datos.
4. Publica el puerto 80 de Nextcloud en el puerto 8080 del anfitrión.

```bash

```

---


## Ejercicio 11. Instalar *mariadb/joomla*

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


## Ejercicio 12. Instalar *mariadb/drupal*

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

## Ejercicio 13. Instalar una aplicación *redis/python*

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
docker search httpd
docker pull httpd:2.4
docker images httpd
docker image inspect httpd:2.4
docker image tag httpd:2.4 mi-httpd:latest
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
docker network create --driver bridge --subnet 172.28.0.0/16 --gateway 172.28.0.1 red1
docker network create --driver bridge red2
docker run -dit --name u1 --hostname host1 --network red1 --ip 172.28.0.10 ubuntu:20.04
docker run -dit --name u2 --hostname host2 --network red2 ubuntu:20.04
docker exec u1 apt-get update
docker exec u1 apt-get install -y inetutils-ping
docker exec u2 apt-get update
docker exec u2 apt-get install -y inetutils-ping
docker exec u1 ping -c 3 host2
docker network connect red2 u1
docker exec u1 ping -c 3 host2
```

El primer `ping` debe fallar porque los contenedores están en redes distintas. El último debe resolver `host2` y alcanzar el contenedor.

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
