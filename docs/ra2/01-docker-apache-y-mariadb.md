# Docker: Apache y Mariadb

## Docker y apache

Vamos a seguir la sesión 1 del curso que está aquí: <https://iesgn.github.io/curso_docker_2021>.

!!! warning "Los ejercicios del curso no"
    No vamos a hacer la sección *Ejercicio* que está en el curso. Vamos a hacer el siguiente que es una versión modificada de aquel.

Tenemos que hacer los pasos indicados aquí: https://iesgn.github.io/curso_docker_2021/sesion1/web.html modificando un poco los pasos.

Voy a pedir una serie de capturas de pantalla, que corresponden con los diferentes pasos. Por favor, sube los ficheros de las capturas de pantalla con los nombres que se piden.


1. Captura de pantalla llamada `1-apache-arrancado-con-docker.jpg` donde se vea la creación del contenedor y podamos comprobar que el contenedor está funcionando. Tiene que funcionar en el puerto 8181, y no en el 8080, como está descrito en la práctica.
1. Captura de pantalla llamada `2-apache-funcionando.jpg` donde se vea el acceso al servidor web utilizando un navegador web (recuerda que tienes que acceder a la ip del ordenador donde tengas instalado docker)
1. Captura de pantalla llamada `3-apache-mi-web.jpg`, donde muestres un fichero html que diga "Hola, soy &lt;tu nombre&gt;", para demostrar que has insertado un fichero personalizado en el servidor apache del contenedor.
1. Captura de pantalla llamada `4-apache-eliminar.jpg`, donde se vea cómo se elimina el contenedor (recuerda que antes debe estar parado el contenedor)

## Docker y mariadb

Y continuamos por este otro enlace: https://iesgn.github.io/curso_docker_2021/sesion1/configuracion.html

1. Captura de pantalla llamada: `5-mariadb-basedatos-pizzeria.jpg`, donde se vea que has creado una base de datos llamada "pizzeria" mediante una terminal.
1. Captura de pantalla llamada: `6-mariadb-tabla-pizzas.jpg`, donde se vea que haces un select de una tabla llamada "pizzas" y alguna pizza. Al menos tiene que haber tres campos: id, nombre y descripcion. Este select lo tienes que hacer mediante la terminal.
1. Introduce un par de pizzas con la orden *insert*. Haz una captura de pantalla `7-mariadb-insertar-pizzas.jpg` donde se vea cómo lo haces con la terminal.
1. Captura de pantalla llamada: `8-mariadb-dbeaver.jpg`. Haz una captura de pantalla donde utilices un cliente gráfico de base de datos de mariadb para acceder a la base de datos, por ejemplo: DB Beaver.