# Prácticas guiadas con Apache

## Objetivo y condiciones de trabajo

Estas prácticas convierten los ejercicios del documento **Ejercicios con Apache** en un recorrido guiado basado en los apartados 3.1 a 3.12 del manual [**LAMP Stack en Debian Server**](https://josejuansanchez.org/iaw/practica-01-01-teoria/).

Trabaja sobre una máquina virtual Debian Server de pruebas. Algunas prácticas modifican la configuración y los sitios activos del servidor. Por tanto, antes de comenzar cada prácticas, haz una instantánea de la máquina virtual para restaurarla cuando termines.

Todos los comandos se ejecutan en el servidor salvo que se indique **CLIENTE**. Sustituye los valores entre `< >` por los datos de tu entorno.

En los ficheros de Apache, las líneas que empiezan por `#` son comentarios. Tras modificar la configuración, comprueba siempre la sintaxis antes de reiniciar:

```bash
# Comprueba la sintaxis de la configuración cargada de Apache.
sudo apache2ctl configtest
```

El resultado correcto es `Syntax OK`.

---

## Práctica 0. Instalar Apache

**Referencia del manual:** [3.1 Instalación de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#instalaci%C3%B3n-de-apache).

Para instalar Apache en Ubuntu Server, ejecuta:

```bash
# Instala el servidor web Apache.
sudo apt install apache2 -y
```

Para instalar PHP en modo FPM y permitir la conexión con MariaDB/MySQL, ejecuta:

```bash
# Instala PHP-FPM y el conector de PHP para MySQL/MariaDB.
sudo apt install php-fpm php-mysql -y
# Habilita los módulos necesarios para conectar Apache con PHP-FPM.
sudo a2enmod proxy_fcgi setenvif
```

Activa la configuración de PHP-FPM. Sustituye `php8.4-fpm` por la versión instalada en tu sistema si fuera diferente. Puedes consultar las configuraciones disponibles con `ls /etc/apache2/conf-available/`:

```bash
# Habilita la configuración de PHP-FPM para Apache.
sudo a2enconf php8.4-fpm
# Reinicia Apache para aplicar la configuración de PHP-FPM.
sudo systemctl restart apache2
```

---

## Práctica 1. Modificar los puertos HTTP y HTTPS de Apache

**Referencia del manual:** [3.4 Cómo modificar el puerto por defecto de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-modificar-el-puerto-por-defecto-de-apache).

### Objetivo de la práctica 1

Cambiar el puerto HTTP de `80` a `8000` y el puerto HTTPS de `443` a `8443`.

### Pasos de la práctica 1

1. Comprueba que Apache está instalado y funcionando:

   ```bash
   # Actualiza el índice local de paquetes disponibles.
   sudo apt update
   # Instala Apache si todavía no está instalado.
   sudo apt install apache2 -y
   # Muestra el estado actual del servicio Apache.
   sudo systemctl status apache2
   ```

2. Edita `/etc/apache2/ports.conf`. Cambia la directiva HTTP:

   ```apache
   # Hace que Apache escuche las conexiones HTTP en el puerto 8000.
   Listen 8000
   ```

   Mantén el bloque `IfModule` de SSL y cambia dentro de él `Listen 443` por `Listen 8443`. Si también aparece un bloque `IfModule mod_gnutls.c`, asegúrate de que no haya dos directivas `Listen 8443` activas.

3. Edita `/etc/apache2/sites-available/000-default.conf` y cambia la primera línea del sitio:

   ```apache
   # Define el sitio virtual HTTP para todas las interfaces en el puerto 8000.
   <VirtualHost *:8000>
   ```

4. Edita `/etc/apache2/sites-available/default-ssl.conf` y cambia **solo el puerto**, del 443 pon **8443**:

   ```apache
   # Define el sitio virtual HTTPS en el puerto 8443.
   <VirtualHost *:8443>
   ```

5. Activa SSL y el sitio HTTPS:

   ```bash
   # Habilita el módulo SSL de Apache.
   sudo a2enmod ssl
   # Habilita el sitio virtual HTTPS predeterminado.
   sudo a2ensite default-ssl.conf
   ```

6. Comprueba la configuración y reinicia Apache:

   ```bash
   # Comprueba que la configuración de Apache no tenga errores de sintaxis.
   sudo apache2ctl configtest
   # Reinicia Apache para aplicar los cambios de puertos y sitios.
   sudo systemctl restart apache2
   ```

7. Comprueba los dos puertos desde el servidor:

   ```bash
   # Envía una petición HTTP al puerto 8000 y muestra solo las cabeceras.
   curl -I http://127.0.0.1:8000
   # Prueba HTTPS en el puerto 8443 y omite la validación del certificado local.
   curl -k -I https://127.0.0.1:8443
   # Muestra los puertos TCP en escucha y los procesos asociados a Apache.
   sudo ss -tlnp | grep apache2
   ```

   La orden `ss` muestra los puertos TCP en los que escucha Apache y el proceso asociado. Sirve para comprobar que está atendiendo en los puertos configurados, como `8000` y `8443`.

### Resultado esperado de la práctica 1

Apache responde en `http://<IP_SERVIDOR>:8000` y `https://<IP_SERVIDOR>:8443`. La opción `-k` permite probar el certificado local autofirmado.

### Captura de pantalla de la práctica 1

Envía una captura de pantalla de la ejecución del último bloque de órdenes de los pasos de esta práctica para comprobar que realmente has cambiado los puertos y habilitado la seguridad.

---

## Práctica 2. Cambiar el directorio por defecto y mostrar su contenido

**Referencia del manual:** [3.5 Cómo modificar el directorio por defecto de Apache para Debian](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-modificar-el-directorio-por-defecto-de-apache).

### Objetivo de la práctica 2

Servir la web desde un directorio distinto de `/var/www/html` y comprobar el listado de archivos con `Options Indexes FollowSymLinks`.

En esta práctica vamos a cambiar el directorio por defecto que tiene los ficheros que se sirven vía web. Además, haremos que cuando no exista el fichero por defecto `index.html` se cargue una web que nos liste el contenido del servidor.

### Pasos de la práctica 2

1. Crea un directorio con dos páginas que no se llamen `index.html`:

   ```bash
   # Crea el directorio que Apache utilizará como raíz del sitio de prueba.
   sudo mkdir -p /home/usuario/misitioweb
   # Crea la primera página de prueba en el directorio.
   echo "Página uno" | sudo tee /home/usuario/misitioweb/uno.html
   # Crea la segunda página de prueba en el directorio.
   echo "Página dos" | sudo tee /home/usuario/misitioweb/dos.html
   # Asigna el directorio y su contenido al usuario y grupo de Apache.
   sudo chown -R www-data:www-data /home/usuario/misitioweb
   # Da permisos de lectura y acceso al directorio personal y al sitio.
   sudo chmod 755 /home/usuario /home/usuario/misitioweb
   ```

2. Edita `/etc/apache2/sites-available/000-default.conf`:

   ```apache
   # Define el sitio virtual HTTP que atiende en el puerto 80.
   <VirtualHost *:80>
        # Indica el contacto administrativo del sitio.
      ServerAdmin webmaster@localhost
        # Selecciona el directorio desde el que se sirven los archivos.
      DocumentRoot /home/usuario/misitioweb

        # Comienza las opciones y permisos para el directorio del sitio.
      <Directory /home/usuario/misitioweb>
            # Permite listar directorios y seguir enlaces simbólicos.
           Options Indexes FollowSymLinks
            # Impide que archivos .htaccess sobrescriban esta configuración.
           AllowOverride None
            # Permite el acceso al contenido a cualquier cliente.
           Require all granted
         # Cierra la configuración específica del directorio.
       </Directory>

         # Define el archivo donde se registran los errores del sitio.
       ErrorLog ${APACHE_LOG_DIR}/error.log
         # Registra las peticiones con el formato combined.
       CustomLog ${APACHE_LOG_DIR}/access.log combined
      # Cierra la definición del sitio virtual.
   </VirtualHost>
   ```

3. Comprueba y aplica los cambios:

   ```bash
   # Comprueba la sintaxis de la configuración antes de aplicarla.
   sudo apache2ctl configtest
   # Reinicia Apache para cargar el nuevo sitio y sus opciones.
   sudo systemctl restart apache2
   ```

4. Abre `http://<IP_SERVIDOR>/` o compruébalo con:

   ```bash
   # Solicita la página raíz al servidor local y muestra la respuesta.
   curl http://127.0.0.1/
   ```

### Resultado esperado de la práctica 2

Apache muestra un listado del directorio con `uno.html` y `dos.html`, porque no existe un archivo `index.html` y se ha habilitado `Indexes`.

### Captura de pantalla de la práctica 2

Envía una captura donde aparezca tu navegador web visualizando los dos ficheros `uno.html` y `dos.html`.

---

## Práctica 3. Habilitar y deshabilitar un módulo de Apache

**Referencia del manual:** [3.6 Cómo habilitar/deshabilitar un módulo de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-habilitardeshabilitar-un-m%C3%B3dulo-de-apache).

### Objetivo de la práctica 3

Habilitar `mod_rewrite` y configurar una regla para que las peticiones a `antes.html` redirijan al navegador a `despues.html`. Al final, deshabilitar el módulo y verificar su estado.

### Pasos de la práctica 3

1. Comprueba si el módulo está cargado:

   ```bash
   # Busca en los módulos cargados si aparece mod_rewrite.
   sudo apache2ctl -M | grep rewrite
   ```

2. Habilítalo y reinicia Apache:

   ```bash
   # Habilita el módulo de reescritura de URL.
   sudo a2enmod rewrite
   # Reinicia Apache para cargar el módulo.
   sudo systemctl restart apache2
   # Comprueba que mod_rewrite figura entre los módulos cargados.
   apache2ctl -M | grep rewrite
   ```

   `mod_rewrite` permite que Apache examine y transforme las URL mediante reglas. Según las opciones de cada regla, puede cambiar internamente el recurso solicitado o enviar al navegador una redirección HTTP. En esta práctica se enviará una redirección temporal: el navegador recibirá la nueva dirección y solicitará `despues.html`. Para que exista una página de destino, créala:

   ```bash
   # Crea la página de destino de la redirección y escribe su contenido.
   echo "Has llegado a la página nueva" | sudo tee /var/www/html/despues.html
   ```

   Edita `/etc/apache2/sites-available/000-default.conf` y añade estas líneas dentro del bloque `<VirtualHost *:80>`:

   ```apache
   # Activa el motor de reescritura para este sitio virtual.
   RewriteEngine On
   # Redirige /antes.html a /despues.html con una respuesta temporal 302.
   RewriteRule ^/antes\.html$ /despues.html [R=302,L]
   ```

   En la regla, `^` y `$` hacen que coincida la ruta completa, y `\.` representa un punto literal. La opción `R=302` indica al navegador que siga una redirección temporal; `L` indica que esta es la última regla que debe procesarse para esa petición.

   Comprueba la configuración y reinicia Apache:

   ```bash
   # Comprueba la sintaxis de la regla antes de aplicarla.
   sudo apache2ctl configtest
   # Reinicia Apache para cargar la regla de reescritura.
   sudo systemctl restart apache2
   ```

   Comprueba las cabeceras para verificar la redirección:

   ```bash
   # Solicita /antes.html y muestra la respuesta de redirección sin seguirla.
   curl -I http://127.0.0.1/antes.html
   ```

   La respuesta debe incluir `HTTP/1.1 302 Found` y `Location: /despues.html`. `curl -I` muestra esa primera respuesta, pero no sigue la redirección; un navegador sí solicitará después la página de destino. También puedes abrir `http://<IP_SERVIDOR>/antes.html` en el navegador y comprobar que termina mostrando el contenido de `despues.html`. No hace falta que exista un archivo `antes.html`: la regla responde a esa ruta antes de que Apache intente servir un archivo con ese nombre. Sin `mod_rewrite`, Apache no puede procesar esta regla.

3. Elimina las dos líneas `RewriteEngine` y `RewriteRule` del sitio virtual, comprueba que la configuración sigue siendo válida y deshabilita el módulo:

   ```bash
   # Comprueba la sintaxis después de quitar las directivas de reescritura.
   sudo apache2ctl configtest
   # Deshabilita el módulo mod_rewrite.
   sudo a2dismod rewrite
   # Reinicia Apache para aplicar la desactivación del módulo.
   sudo systemctl restart apache2
   # Comprueba si rewrite sigue cargado; true evita error si grep no encuentra coincidencias.
   apache2ctl -M | grep rewrite || true
   ```

### Resultado esperado de la práctica 3

Después de `a2enmod` aparece `rewrite_module (shared)`. Con la regla configurada, una petición a `/antes.html` devuelve una redirección `302` hacia `/despues.html`, cuyo contenido muestra el navegador. Después de eliminar la regla y ejecutar `a2dismod`, el módulo deja de aparecer.

### Captura de pantalla de la práctica 3

La captura de pantalla será ejecutar la siguiente orden y que se vea que `Location` es `despues.html`. 

   ```bash
   # Solicita /antes.html y muestra la respuesta de redirección sin seguirla.
   curl -I http://127.0.0.1/antes.html
   ```

---

## Práctica 4. Configurar `DirectoryIndex`

**Referencia del manual:** [3.7, Cómo configurar la directiva DirectoryIndex](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-configurar-la-directiva-directoryindex).

### Objetivo de la práctica 4

Crear `index.html` e `index.php` y controlar cuál se carga primero cuando se visita el directorio sin indicar un archivo.

### Pasos de la práctica 4

1. Crea los dos archivos:

   ```bash
   # Crea el directorio de la práctica.
   sudo mkdir -p /var/www/html/práctica4
   # Escribe el contenido de prueba en la página HTML.
   echo "Contenido HTML" | sudo tee /var/www/html/práctica4/index.html
   # Escribe un ejemplo PHP en la página PHP.
   printf '%s\n' '<?php echo "Contenido PHP"; ?>' | sudo tee /var/www/html/práctica4/index.php
   ```

2. Edita `/etc/apache2/mods-available/dir.conf` y coloca primero `index.php`:

   ```apache
   # Da prioridad a index.php al solicitar el directorio.
   DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
   ```

3. Comprueba y reinicia:

   ```bash
   # Comprueba la sintaxis de Apache antes de reiniciar.
   sudo apache2ctl configtest
   # Reinicia Apache para aplicar el orden de DirectoryIndex.
   sudo systemctl restart apache2
   ```

4. Visita `http://<IP_SERVIDOR>/práctica4/` y observa el contenido.

   También puedes solicitarlo desde el servidor con Bash:

   ```bash
   # Solicita el directorio y muestra la página índice que Apache selecciona.
   curl http://127.0.0.1/pr%C3%A1ctica4/
   ```

5. Intercambia el orden:

   ```apache
   # Da prioridad a index.html al solicitar el directorio.
   DirectoryIndex index.html index.php index.cgi index.pl index.xhtml index.htm
   ```

   Comprueba de nuevo:

   ```bash
   # Comprueba la sintaxis de Apache antes de reiniciar.
   sudo apache2ctl configtest
   # Reinicia Apache para aplicar el nuevo orden de DirectoryIndex.
   sudo systemctl restart apache2
   ```

### Resultado esperado de la práctica 4

Cuando `index.php` aparece primero, se muestra `Contenido PHP`. Cuando `index.html` aparece primero, se muestra `Contenido HTML`.

### Captura de pantalla de la práctica 4

Deja `index.php` primero en la directiva `DirectoryIndex` y envía una captura del navegador al entrar en `http://<IP_SERVIDOR>/práctica4/` sin indicar el nombre de ningún archivo. En la captura debe verse `Contenido PHP`, que confirma que Apache carga `index.php` antes que `index.html`.

---

## Práctica 5. Crear hosts virtuales basados en el dominio

**Referencia del manual:** [3.8, Cómo crear un nuevo host virtual](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-crear-un-nuevo-host-virtual) y [3.8.1, Host virtual basado en el nombre de dominio](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#host-virtual-basado-en-el-nombre-de-dominio).

### Objetivo de la práctica 5

Configurar dos sitios en el mismo Apache y diferenciarlos por el nombre de dominio.

### Pasos de la práctica 5

1. Crea los directorios y las páginas de prueba:

   ```bash
   # Crea los directorios raíz de los dos sitios de prueba.
   sudo mkdir -p /var/www/html/web1 /var/www/html/web2
   # Crea la página inicial del primer sitio.
   echo "Sitio web 1" | sudo tee /var/www/html/web1/index.html
   # Crea la página inicial del segundo sitio.
   echo "Sitio web 2" | sudo tee /var/www/html/web2/index.html
   # Asigna ambos sitios al usuario y grupo de Apache.
   sudo chown -R www-data:www-data /var/www/html/web1 /var/www/html/web2
   ```

2. Crea `/etc/apache2/sites-available/web1.conf`:

   ```apache
      # Define el sitio virtual HTTP para todas las interfaces en el puerto 80.
   <VirtualHost *:80>
             # Asocia este sitio con el nombre web1.com.
          ServerName web1.com
         # Indica el correo del administrador del primer sitio.
          ServerAdmin webmaster@web1.com
         # Define la raíz de documentos del primer sitio.
       DocumentRoot /var/www/html/web1
         # Selecciona el archivo de registro de errores del primer sitio.
       ErrorLog ${APACHE_LOG_DIR}/web1-error.log
         # Registra los accesos al primer sitio con formato combined.
       CustomLog ${APACHE_LOG_DIR}/web1-access.log combined
      # Cierra la definición del sitio virtual.
   </VirtualHost>
   ```

3. Crea `/etc/apache2/sites-available/web2.conf` cambiando el dominio y el directorio:

   ```apache
      # Define el sitio virtual HTTP para todas las interfaces en el puerto 80.
   <VirtualHost *:80>
         # Asocia este sitio con el nombre web2.com.
       ServerName web2.com
         # Indica el correo del administrador del segundo sitio.
       ServerAdmin webmaster@web2.com
         # Define la raíz de documentos del segundo sitio.
       DocumentRoot /var/www/html/web2
         # Selecciona el archivo de registro de errores del segundo sitio.
       ErrorLog ${APACHE_LOG_DIR}/web2-error.log
         # Registra los accesos al segundo sitio con formato combined.
       CustomLog ${APACHE_LOG_DIR}/web2-access.log combined
      # Cierra la definición del sitio virtual.
   </VirtualHost>
   ```

4. Habilita los sitios, deshabilita el sitio por defecto y recarga Apache:

   ```bash
   # Deshabilita el sitio virtual predeterminado.
   sudo a2dissite 000-default.conf
   # Habilita los sitios virtuales web1 y web2.
   sudo a2ensite web1.conf web2.conf
   # Comprueba que la configuración de ambos sitios es válida.
   sudo apache2ctl configtest
   # Recarga Apache para activar los sitios sin detener el servicio.
   sudo systemctl reload apache2
   ```

5. En el servidor, añade estas líneas al final de `/etc/hosts` para que ambos nombres apunten a la dirección local:

   ```text
   # Asocia la dirección local con el nombre del primer sitio.
   127.0.0.1 web1.com
   # Asocia la dirección local con el nombre del segundo sitio.
   127.0.0.1 web2.com
   ```

   `127.0.0.1` es la dirección de loopback: esta configuración sirve para probar desde el propio servidor. Si accedes desde otro equipo, debes usar la IP del servidor en el archivo `hosts` de ese equipo.

6. Prueba ambos sitios:

   ```bash
   # Solicita la portada del primer dominio virtual.
   curl http://web1.com/
   # Solicita la portada del segundo dominio virtual.
   curl http://web2.com/
   ```

### Resultado esperado de la práctica 5

`web1.com` devuelve `Sitio web 1` y `web2.com` devuelve `Sitio web 2`, aunque ambos nombres resuelvan hacia `127.0.0.1`.

### Captura de pantalla de la práctica 5

Envía el resultado de la ejecución de estas órdenes:

   ```bash
   # Solicita la portada del primer dominio virtual.
   curl http://web1.com/
   # Solicita la portada del segundo dominio virtual.
   curl http://web2.com/
   ```

---

## Práctica 6. Crear hosts virtuales basados en el puerto

**Referencia del manual:** [3.8, Cómo crear un nuevo host virtual](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-crear-un-nuevo-host-virtual) y [3.8.2, Host virtual basado en el puerto](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#host-virtual-basado-en-el-puerto).

### Objetivo de la práctica 6

Servir dos sitios distintos usando dos puertos diferentes.

### Pasos de la práctica 6

1. Crea los directorios y páginas:

   ```bash
   # Crea los directorios raíz para los sitios de los puertos 8000 y 8001.
   sudo mkdir -p /var/www/html/web-port-8000 /var/www/html/web-port-8001
   # Crea la página de prueba del puerto 8000.
   echo "Sitio del puerto 8000" | sudo tee /var/www/html/web-port-8000/index.html
   # Crea la página de prueba del puerto 8001.
   echo "Sitio del puerto 8001" | sudo tee /var/www/html/web-port-8001/index.html
   # Asigna ambos directorios al usuario y grupo de Apache.
   sudo chown -R www-data:www-data /var/www/html/web-port-8000 /var/www/html/web-port-8001
   ```

2. Añade estas líneas al final de `/etc/apache2/ports.conf`:

   ```apache
   # Hace que Apache escuche peticiones en el puerto TCP 8000.
   Listen 8000
   # Hace que Apache escuche peticiones en el puerto TCP 8001.
   Listen 8001
   ```

3. Crea `/etc/apache2/sites-available/puerto-8000.conf`:

   ```apache
      # Define el sitio virtual asociado al puerto 8000.
   <VirtualHost *:8000>
         # Selecciona la raíz de documentos del sitio del puerto 8000.
       DocumentRoot /var/www/html/web-port-8000
         # Comienza la configuración de acceso al directorio del sitio.
       <Directory /var/www/html/web-port-8000>
            # Permite que todos los clientes accedan a sus archivos.
           Require all granted
         # Cierra la configuración de acceso al directorio.
       </Directory>
         # Selecciona el registro de errores del sitio.
       ErrorLog ${APACHE_LOG_DIR}/puerto-8000-error.log
         # Registra las peticiones al sitio con formato combined.
       CustomLog ${APACHE_LOG_DIR}/puerto-8000-access.log combined
      # Cierra la definición del sitio virtual.
   </VirtualHost>
   ```

4. Crea `/etc/apache2/sites-available/puerto-8001.conf` usando el puerto `8001` y el directorio `/var/www/html/web-port-8001`.

5. Habilita los sitios y aplica la configuración:

   ```bash
   # Habilita los sitios virtuales de los puertos 8000 y 8001.
   sudo a2ensite puerto-8000.conf puerto-8001.conf
   # Comprueba la sintaxis antes de aplicar los sitios.
   sudo apache2ctl configtest
   # Recarga Apache para activar ambos sitios virtuales.
   sudo systemctl reload apache2
   ```

6. Comprueba los dos sitios:

   ```bash
   # Solicita el sitio configurado para el puerto 8000.
   curl http://127.0.0.1:8000/
   # Solicita el sitio configurado para el puerto 8001.
   curl http://127.0.0.1:8001/
   ```

### Resultado esperado de la práctica 6

El puerto `8000` devuelve `Sitio del puerto 8000` y el puerto `8001` devuelve `Sitio del puerto 8001`.


### Captura de pantalla de la práctica 5

Envía el resultado de la ejecución de estas órdenes:

   ```bash
   # Solicita el sitio configurado para el puerto 8000.
   curl http://127.0.0.1:8000/
   # Solicita el sitio configurado para el puerto 8001.
   curl http://127.0.0.1:8001/
   ```

---

## Práctica 7. Consultar los hosts virtuales activos

**Referencia del manual:** [3.9, Cómo consultar los hosts virtuales activos](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-consultar-los-hosts-virtuales-activos).

### Objetivo de la práctica 7

Distinguir los sitios configurados de los sitios realmente habilitados.

### Pasos de la práctica 7

1. Lista los ficheros de sitios disponibles:

   ```bash
   # Lista los ficheros de sitios disponibles, estén o no habilitados.
   ls -l /etc/apache2/sites-available/
   ```

2. Lista los sitios habilitados mediante enlaces simbólicos:

   ```bash
   # Lista los sitios habilitados mediante enlaces simbólicos.
   ls -l /etc/apache2/sites-enabled/
   ```

3. Consulta cómo Apache interpreta los hosts virtuales:

   ```bash
   # Muestra cómo Apache interpreta los hosts virtuales activos.
   sudo apache2ctl -S
   ```

4. Anota para cada sitio su puerto, `ServerName`, `DocumentRoot` y fichero de configuración.

### Resultado esperado de la práctica 7

`sites-available` puede contener sitios que no están activos. Solo los que aparecen enlazados en `sites-enabled` participan en la configuración cargada por Apache. `apache2ctl -S` muestra los hosts virtuales activos y el sitio predeterminado de cada puerto.

### Captura de pantalla de la práctica 7

Incluye la salida de `apache2ctl -S`. Piensa  por qué un sitio disponible pero no habilitado no recibe peticiones y pon un comentario en la práctica que responda a esta pregunta así:

   ```text
   Práctica 7
   Un sitio disponible pero no habilitado no recibe peticiones porque...
   ```

---

## Práctica 8. Comprobar la sintaxis y localizar un error

**Referencia del manual:** [3.10, Cómo comprobar la sintaxis de los archivos de configuración de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-comprobar-la-sintaxis-de-los-archivos-de-configuraci%C3%B3n-de-apache).

### Objetivo de la práctica 8

Provocar un error controlado, localizarlo con `configtest` y corregirlo sin dejar Apache inutilizado.

### Pasos de la práctica 8

1. Ejecuta la comprobación con la configuración correcta:

   ```bash
   # Comprueba la sintaxis de la configuración antes de introducir el error.
   sudo apache2ctl configtest
   ```

2. Edita un fichero de sitio habilitado, por ejemplo:

   ```bash
   # Abre el fichero del sitio virtual habilitado para editarlo.
   sudo nano /etc/apache2/sites-available/000-default.conf
   ```

3. Introduce un error sencillo y controlado, como cambiar:

   ```apache
   # Ejemplo de apertura correcta del bloque del sitio virtual.
   <VirtualHost *:80>
   ```

   por:

   ```apache
   # Ejemplo de apertura incorrecta: falta el carácter > final.
   <VirtualHost *:80
   ```

4. Ejecuta la comprobación sin reiniciar Apache:

   ```bash
   # Detecta el error de sintaxis sin reiniciar Apache.
   sudo apache2ctl configtest
   ```

   Guarda el mensaje de error y la línea indicada.

5. Corrige el fichero, vuelve a ejecutar `configtest` y solo cuando aparezca `Syntax OK`, recarga Apache:

   ```bash
   # Comprueba que el error se ha corregido.
   sudo apache2ctl configtest
   # Recarga Apache solo después de obtener Syntax OK.
   sudo systemctl reload apache2
   ```

### Resultado esperado de la práctica 8

Mientras existe el error, `configtest` informa de un error de sintaxis. Tras corregirlo, muestra `Syntax OK`. Un error de sintaxis no debe aplicarse reiniciando el servicio.

###

---

## Práctica 9. Ocultar la versión de Apache

**Referencia del manual:** [3.11, Cómo ocultar la versión de Apache al cliente](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-ocultar-la-versi%C3%B3n-de-apache-al-cliente).

### Objetivo de la práctica 9

Reducir la información del servidor expuesta en las respuestas HTTP.

### Pasos de la práctica 9

1. Edita `/etc/apache2/conf-available/security.conf` y deja activas estas directivas:

   ```apache
   # Reduce la información de versión que Apache incluye en las respuestas.
   ServerTokens Prod
   # Oculta la firma de Apache en las páginas generadas por el servidor.
   ServerSignature Off
   ```

   `ServerTokens` es una directiva global. `ServerSignature` también puede configurarse para un host virtual.

2. Comprueba y recarga Apache:

   ```bash
   # Comprueba la sintaxis de los cambios de seguridad.
   sudo apache2ctl configtest
   # Recarga Apache para aplicar la configuración.
   sudo systemctl reload apache2
   ```

3. Consulta las cabeceras que recibe el cliente:

   ```bash
   # Muestra las cabeceras HTTP de la respuesta del servidor local.
   curl -I http://127.0.0.1/
   # Muestra el intercambio detallado y filtra la cabecera Server.
   curl -Iv http://127.0.0.1/ 2>&1 | grep -i server
   ```

### Resultado esperado de la práctica 9

La cabecera `Server` no debe mostrar la versión concreta de Apache, por ejemplo `Apache/2.4.x`. Las páginas de error tampoco deben mostrar la firma completa del servidor.

### Captura de pantalla de la práctica 9

Captura de pantalla de las últimas órdenes ejecutadas:

   ```bash
   # Muestra las cabeceras HTTP de la respuesta del servidor local.
   curl -I http://127.0.0.1/
   # Muestra el intercambio detallado y filtra la cabecera Server.
   curl -Iv http://127.0.0.1/ 2>&1 | grep -i server
   ```

---

## Práctica 10. Consultar y aumentar el detalle de los logs

**Referencia del manual:** [3.12, Archivos de log de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#archivos-de-log-de-apache) y sus apartados 3.12.1, 3.12.2 y 3.12.3.

### Objetivo de la práctica 10

Localizar los logs, observar accesos correctos y errores 404, y aumentar el nivel de detalle.

### Pasos de la práctica 10

1. Identifica los ficheros de log:

   ```bash
   # Lista los ficheros de log de Apache con sus detalles.
   sudo ls -l /var/log/apache2/
   # Muestra las diez líneas más recientes del registro de accesos.
   sudo tail -n 10 /var/log/apache2/access.log
   # Muestra las diez líneas más recientes del registro de errores.
   sudo tail -n 10 /var/log/apache2/error.log
   ```

2. En una terminal del servidor, observa el registro de accesos en tiempo real:

   ```bash
   # Sigue el registro de accesos en tiempo real hasta pulsar Ctrl+C.
   sudo tail -f /var/log/apache2/access.log
   ```

3. En otra terminal, solicita una página existente y otra inexistente:

   ```bash
   # Solicita la página inicial para generar una petición correcta.
   curl -I http://127.0.0.1/
   # Solicita una ruta inexistente para generar una respuesta 404.
   curl -I http://127.0.0.1/pagina-que-no-existe.html
   ```

   Vuelve a la terminal de `tail` y anota los códigos `200` y `404`. Pulsa `Ctrl+C` para detener el seguimiento.

4. Cambia el nivel de log en `/etc/apache2/apache2.conf`:

   ```bash
   # Abre el archivo principal de Apache para editar el nivel de log.
   sudo nano /etc/apache2/apache2.conf
   ```

   Busca `LogLevel` o añade al final:

   ```apache
   # Activa el nivel de detalle debug para el registro de Apache.
   LogLevel debug
   ```

5. Comprueba y recarga Apache:

   ```bash
   # Comprueba la sintaxis del nuevo nivel de registro.
   sudo apache2ctl configtest
   # Recarga Apache para aplicar el nivel debug.
   sudo systemctl reload apache2
   ```

6. Repite varias peticiones, consulta el log de errores y compara el detalle:

   ```bash
   # Genera una petición correcta para observar su registro.
   curl -I http://127.0.0.1/
   # Genera una respuesta 404 para observar su registro.
   curl -I http://127.0.0.1/pagina-que-no-existe.html
   # Muestra las treinta líneas más recientes del registro de errores.
   sudo tail -n 30 /var/log/apache2/error.log
   ```

### Resultado esperado de la práctica 10

`access.log` registra las peticiones recibidas, incluidas las respuestas `200` y `404`. `error.log` registra los errores del servidor. Con `LogLevel debug` se obtiene mucho más detalle que con `warn`.

### Precaución de la práctica 10

No mantengas `LogLevel debug` en un servidor de producción: genera mucho volumen de información y puede dificultar la lectura de los problemas reales.

### Captura de pantalla de la práctica 10

Envía una captura de pantalla donde se vean los logs con el nivel `LogLevel debug`.

---
