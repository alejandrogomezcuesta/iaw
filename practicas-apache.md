# Prácticas guiadas con Apache

## Objetivo y condiciones de trabajo

Estas prácticas convierten los ejercicios del documento **Ejercicios con Apache** en un recorrido guiado basado en los apartados 3.1 a 3.12 del manual [**LAMP Stack en Debian Server**](https://josejuansanchez.org/iaw/practica-01-01-teoria/).

Trabaja sobre una máquina virtual Debian Server con Apache instalado. Cada ejercicio parte de una configuración limpia de Apache. Cuando sea posible, crea una instantánea de VirtualBox después de instalar Apache y vuelve a ella antes de comenzar el ejercicio siguiente.

Todos los comandos se ejecutan en el servidor salvo que se indique **CLIENTE**. Sustituye los valores entre `< >` por los datos de tu entorno.

Antes de modificar un fichero de configuración, guarda una copia:

```bash
sudo cp fichero fichero.bak
```

En los ficheros de Apache, las líneas que empiezan por `#` son comentarios. Tras modificar la configuración, comprueba siempre la sintaxis antes de reiniciar:

```bash
sudo apache2ctl configtest
```

El resultado correcto es `Syntax OK`.

---

## Práctica 0. Instalar Apache

**Referencia del manual:** [3.1 Instalación de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#instalaci%C3%B3n-de-apache).

Para instalar Apache en Ubuntu Server, ejecuta:

```bash
sudo apt install apache2 -y
```

Para instalar PHP en modo FPM y permitir la conexión con MariaDB/MySQL, ejecuta:

```bash
sudo apt install php-fpm php-mysql -y
sudo a2enmod proxy_fcgi setenvif
```

Activa la configuración de PHP-FPM. Sustituye `php8.4-fpm` por la versión instalada en tu sistema si fuera diferente. Puedes consultar las configuraciones disponibles con `ls /etc/apache2/conf-available/`:

```bash
sudo a2enconf php8.4-fpm
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
   sudo apt update
   sudo apt install apache2 -y
   sudo systemctl status apache2
   ```

2. Guarda copias de los ficheros que vas a modificar:

   ```bash
   sudo cp /etc/apache2/ports.conf /etc/apache2/ports.conf.bak
   sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/000-default.conf.bak
   sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf.bak
   ```

3. Edita `/etc/apache2/ports.conf`. Cambia la directiva HTTP:

   ```apache
   Listen 8000
   ```

   Mantén el bloque `IfModule` de SSL y cambia dentro de él `Listen 443` por `Listen 8443`. Si también aparece un bloque `IfModule mod_gnutls.c`, asegúrate de que no haya dos directivas `Listen 8443` activas.

4. Edita `/etc/apache2/sites-available/000-default.conf` y cambia la primera línea del sitio:

   ```apache
   <VirtualHost *:8000>
   ```

5. Edita `/etc/apache2/sites-available/default-ssl.conf` y cambia:

   ```apache
   <IfModule mod_ssl.c>
       <VirtualHost *:8443>
   ```

6. Activa SSL y el sitio HTTPS:

   ```bash
   sudo a2enmod ssl
   sudo a2ensite default-ssl.conf
   ```

7. Comprueba la configuración y reinicia Apache:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl restart apache2
   ```

8. Comprueba los dos puertos desde el servidor:

   ```bash
   curl -I http://127.0.0.1:8000
   curl -k -I https://127.0.0.1:8443
   sudo ss -tlnp | grep apache2
   ```

   La orden `ss` muestra los puertos TCP en los que escucha Apache y el proceso asociado. Sirve para comprobar que está atendiendo en los puertos configurados, como `8000` y `8443`.

### Resultado esperado de la práctica 1

Apache responde en `http://<IP_SERVIDOR>:8000` y `https://<IP_SERVIDOR>:8443`. La opción `-k` permite probar el certificado local autofirmado.

### Recuperación de la práctica 1

Restaura las copias, vuelve a dejar `Listen 80`, `Listen 443`, `<VirtualHost *:80>` y `<VirtualHost *:443>`, deshabilita SSL si lo habilitaste solo para esta práctica y reinicia Apache.

---

## Práctica 2. Cambiar el directorio por defecto y mostrar su contenido

**Referencia del manual:** [3.5, Cómo modificar el directorio por defecto de Apache para Debian](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-modificar-el-directorio-por-defecto-de-apache).

### Objetivo de la práctica 2

Servir la web desde un directorio distinto de `/var/www/html` y comprobar el listado de archivos con `Options Indexes FollowSymLinks`.

### Pasos de la práctica 2

1. Crea un directorio con dos páginas que no se llamen `index.html`:

   ```bash
   sudo mkdir -p /home/usuario/misitioweb
   echo "Página uno" | sudo tee /home/usuario/misitioweb/uno.html
   echo "Página dos" | sudo tee /home/usuario/misitioweb/dos.html
   sudo chown -R www-data:www-data /home/usuario/misitioweb
   sudo chmod 755 /home/usuario /home/usuario/misitioweb
   ```

2. Guarda y edita `/etc/apache2/sites-available/000-default.conf`:

   ```apache
   <VirtualHost *:80>
      ServerAdmin webmaster@localhost
      DocumentRoot /home/usuario/misitioweb

      <Directory /home/usuario/misitioweb>
           Options Indexes FollowSymLinks
           AllowOverride None
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

   Estas son las funciones de las directivas utilizadas:

   - `<VirtualHost *:80>`: define el sitio virtual que atiende las peticiones recibidas en el puerto HTTP 80, en cualquier interfaz de red (`*`).
   - `ServerAdmin webmaster@localhost`: indica la dirección de correo del administrador del sitio. Apache puede mostrarla en algunas páginas de error.
   - `DocumentRoot /home/usuario/misitioweb`: establece el directorio raíz desde el que Apache sirve los archivos de esta web.
   - `<Directory /home/usuario/misitioweb>`: comienza la configuración de permisos y opciones para ese directorio del sistema.
   - `Options Indexes FollowSymLinks`: permite mostrar un listado cuando no hay página de inicio (`Indexes`) y seguir enlaces simbólicos (`FollowSymLinks`).
   - `AllowOverride None`: impide que los archivos `.htaccess` modifiquen esta configuración. Las opciones deben definirse en los archivos de configuración de Apache.
   - `Require all granted`: permite el acceso a todos los clientes que soliciten contenido de este directorio.
   - `ErrorLog ${APACHE_LOG_DIR}/error.log`: indica dónde se guardan los errores de este sitio. `${APACHE_LOG_DIR}` suele corresponder a `/var/log/apache2`.
   - `CustomLog ${APACHE_LOG_DIR}/access.log combined`: guarda las peticiones recibidas en `access.log` usando el formato `combined`, que incluye datos como la IP, la fecha, la URL, el código de respuesta y el navegador.
   - `</Directory>` y `</VirtualHost>`: cierran, respectivamente, los bloques de configuración del directorio y del sitio virtual.

3. Comprueba y aplica los cambios:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl restart apache2
   ```

4. Abre `http://<IP_SERVIDOR>/` o compruébalo con:

   ```bash
   curl http://127.0.0.1/
   ```

### Resultado esperado de la práctica 2

Apache muestra un listado del directorio con `uno.html` y `dos.html`, porque no existe un archivo `index.html` y se ha habilitado `Indexes`.

### Recuperación de la práctica 2

Restaura `/etc/apache2/sites-available/000-default.conf` y reinicia Apache.

---

## Práctica 3. Habilitar y deshabilitar un módulo de Apache

**Referencia del manual:** [3.6, Cómo habilitar/deshabilitar un módulo de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-habilitardeshabilitar-un-m%C3%B3dulo-de-apache).

### Objetivo de la práctica 3

Habilitar y deshabilitar el módulo `rewrite` y verificar su estado.

### Pasos de la práctica 3

1. Comprueba si el módulo está cargado:

   ```bash
   apache2ctl -M | grep rewrite
   ```

2. Habilítalo y reinicia Apache:

   ```bash
   sudo a2enmod rewrite
   sudo systemctl restart apache2
   apache2ctl -M | grep rewrite
   ```

   Hasta este momento solo se ha cargado el módulo. Para comprobar qué permite hacer, crea una página de destino:

   ```bash
   echo "Has llegado a la página nueva" | sudo tee /var/www/html/despues.html
   ```

   Edita `/etc/apache2/sites-available/000-default.conf` y añade estas líneas dentro del bloque `<VirtualHost *:80>`:

   ```apache
   RewriteEngine On
   RewriteRule ^/antes$ /despues.html [R=302,L]
   ```

   Comprueba la configuración y reinicia Apache:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl restart apache2
   ```

   Prueba una dirección que no existe, pero que ahora será redirigida:

   ```bash
   curl -I http://127.0.0.1/antes
   ```

   La respuesta debe incluir `HTTP/1.1 302 Found` y una cabecera `Location: /despues.html`. Sin `mod_rewrite`, la regla no se puede interpretar y Apache no puede aplicar esta redirección.

3. Elimina las dos líneas `RewriteEngine` y `RewriteRule` del sitio virtual, comprueba que la configuración sigue siendo válida y deshabilita el módulo:

   ```bash
   sudo apache2ctl configtest
   sudo a2dismod rewrite
   sudo systemctl restart apache2
   apache2ctl -M | grep rewrite || true
   ```

### Resultado esperado de la práctica 3

Después de `a2enmod` aparece `rewrite_module (shared)`. Con la regla configurada, una petición a `/antes` devuelve una redirección `302` hacia `/despues.html`. Después de eliminar la regla y ejecutar `a2dismod`, el módulo deja de aparecer.

### Recuperación de la práctica 3

Deja el módulo en el estado indicado por el profesor. Si no se indica ninguno, vuelve a habilitarlo y comprueba que Apache sigue funcionando. Puedes eliminar la página de prueba con `sudo rm /var/www/html/despues.html`.

---

## Práctica 4. Configurar `DirectoryIndex`

**Referencia del manual:** [3.7, Cómo configurar la directiva DirectoryIndex](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-configurar-la-directiva-directoryindex).

### Objetivo de la práctica 4

Crear `index.html` e `index.php` y controlar cuál se carga primero cuando se visita el directorio sin indicar un archivo.

### Pasos de la práctica 4

1. Crea los dos archivos:

   ```bash
   sudo mkdir -p /var/www/html/práctica4
   echo "Contenido HTML" | sudo tee /var/www/html/práctica4/index.html
   printf '%s\n' '<?php echo "Contenido PHP"; ?>' | sudo tee /var/www/html/práctica4/index.php
   ```

2. Edita `/etc/apache2/mods-available/dir.conf` y coloca primero `index.php`:

   ```apache
   DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
   ```

3. Comprueba y reinicia:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl restart apache2
   ```

4. Visita `http://<IP_SERVIDOR>/práctica4/` y observa el contenido.

5. Intercambia el orden:

   ```apache
   DirectoryIndex index.html index.php index.cgi index.pl index.xhtml index.htm
   ```

   Comprueba de nuevo:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl restart apache2
   ```

### Resultado esperado de la práctica 4

Cuando `index.php` aparece primero, se muestra `Contenido PHP`. Cuando `index.html` aparece primero, se muestra `Contenido HTML`.

### Recuperación de la práctica 4

Restaura el orden original de `DirectoryIndex` y elimina `/var/www/html/práctica4` si ya no lo necesitas.

---

## Práctica 5. Crear hosts virtuales basados en el dominio

**Referencia del manual:** [3.8, Cómo crear un nuevo host virtual](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-crear-un-nuevo-host-virtual) y [3.8.1, Host virtual basado en el nombre de dominio](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#host-virtual-basado-en-el-nombre-de-dominio).

### Objetivo de la práctica 5

Configurar dos sitios en el mismo Apache y diferenciarlos por el nombre de dominio.

### Pasos de la práctica 5

1. Crea los directorios y las páginas de prueba:

   ```bash
   sudo mkdir -p /var/www/html/web1 /var/www/html/web2
   echo "Sitio web 1" | sudo tee /var/www/html/web1/index.html
   echo "Sitio web 2" | sudo tee /var/www/html/web2/index.html
   sudo chown -R www-data:www-data /var/www/html/web1 /var/www/html/web2
   ```

2. Crea `/etc/apache2/sites-available/web1.conf`:

   ```apache
   <VirtualHost *:80>
       ServerName web1.com
       ServerAdmin webmaster@web1.com
       DocumentRoot /var/www/html/web1
       ErrorLog ${APACHE_LOG_DIR}/web1-error.log
       CustomLog ${APACHE_LOG_DIR}/web1-access.log combined
   </VirtualHost>
   ```

3. Crea `/etc/apache2/sites-available/web2.conf` cambiando el dominio y el directorio:

   ```apache
   <VirtualHost *:80>
       ServerName web2.com
       ServerAdmin webmaster@web2.com
       DocumentRoot /var/www/html/web2
       ErrorLog ${APACHE_LOG_DIR}/web2-error.log
       CustomLog ${APACHE_LOG_DIR}/web2-access.log combined
   </VirtualHost>
   ```

4. Habilita los sitios, deshabilita el sitio por defecto y recarga Apache:

   ```bash
   sudo a2dissite 000-default.conf
   sudo a2ensite web1.conf web2.conf
   sudo apache2ctl configtest
   sudo systemctl reload apache2
   ```

5. En el **CLIENTE**, añade al archivo `/etc/hosts` estas líneas, usando la IP del servidor:

   ```text
   <IP_SERVIDOR> web1.com
   <IP_SERVIDOR> web2.com
   ```

6. Prueba ambos sitios:

   ```bash
   curl http://web1.com/
   curl http://web2.com/
   ```

### Resultado esperado de la práctica 5

`web1.com` devuelve `Sitio web 1` y `web2.com` devuelve `Sitio web 2`, aunque ambos nombres resuelvan hacia la misma IP.

### Recuperación de la práctica 5

Deshabilita `web1.conf` y `web2.conf`, vuelve a habilitar `000-default.conf`, recarga Apache y elimina las entradas de `/etc/hosts` del cliente.

---

## Práctica 6. Crear hosts virtuales basados en el puerto

**Referencia del manual:** [3.8, Cómo crear un nuevo host virtual](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-crear-un-nuevo-host-virtual) y [3.8.2, Host virtual basado en el puerto](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#host-virtual-basado-en-el-puerto).

### Objetivo de la práctica 6

Servir dos sitios distintos usando dos puertos diferentes.

### Pasos de la práctica 6

1. Crea los directorios y páginas:

   ```bash
   sudo mkdir -p /var/www/html/web-port-8000 /var/www/html/web-port-8001
   echo "Sitio del puerto 8000" | sudo tee /var/www/html/web-port-8000/index.html
   echo "Sitio del puerto 8001" | sudo tee /var/www/html/web-port-8001/index.html
   sudo chown -R www-data:www-data /var/www/html/web-port-8000 /var/www/html/web-port-8001
   ```

2. Añade estas líneas al final de `/etc/apache2/ports.conf`:

   ```apache
   Listen 8000
   Listen 8001
   ```

3. Crea `/etc/apache2/sites-available/puerto-8000.conf`:

   ```apache
   <VirtualHost *:8000>
       DocumentRoot /var/www/html/web-port-8000
       <Directory /var/www/html/web-port-8000>
           Require all granted
       </Directory>
       ErrorLog ${APACHE_LOG_DIR}/puerto-8000-error.log
       CustomLog ${APACHE_LOG_DIR}/puerto-8000-access.log combined
   </VirtualHost>
   ```

4. Crea `/etc/apache2/sites-available/puerto-8001.conf` usando el puerto `8001` y el directorio `/var/www/html/web-port-8001`.

5. Habilita los sitios y aplica la configuración:

   ```bash
   sudo a2ensite puerto-8000.conf puerto-8001.conf
   sudo apache2ctl configtest
   sudo systemctl reload apache2
   ```

6. Comprueba los dos sitios:

   ```bash
   curl http://127.0.0.1:8000/
   curl http://127.0.0.1:8001/
   ```

### Resultado esperado de la práctica 6

El puerto `8000` devuelve `Sitio del puerto 8000` y el puerto `8001` devuelve `Sitio del puerto 8001`.

### Recuperación de la práctica 6

Deshabilita ambos sitios, elimina las líneas `Listen 8000` y `Listen 8001` y recarga Apache.

---

## Práctica 7. Consultar los hosts virtuales activos

**Referencia del manual:** [3.9, Cómo consultar los hosts virtuales activos](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-consultar-los-hosts-virtuales-activos).

### Objetivo de la práctica 7

Distinguir los sitios configurados de los sitios realmente habilitados.

### Pasos de la práctica 7

1. Lista los ficheros de sitios disponibles:

   ```bash
   ls -l /etc/apache2/sites-available/
   ```

2. Lista los sitios habilitados mediante enlaces simbólicos:

   ```bash
   ls -l /etc/apache2/sites-enabled/
   ```

3. Consulta cómo Apache interpreta los hosts virtuales:

   ```bash
   sudo apache2ctl -S
   ```

4. Anota para cada sitio su puerto, `ServerName`, `DocumentRoot` y fichero de configuración.

### Resultado esperado de la práctica 7

`sites-available` puede contener sitios que no están activos. Solo los que aparecen enlazados en `sites-enabled` participan en la configuración cargada por Apache. `apache2ctl -S` muestra los hosts virtuales activos y el sitio predeterminado de cada puerto.

### Entrega de la práctica 7

Incluye en tus apuntes la salida de `apache2ctl -S` y explica por qué un sitio disponible pero no habilitado no recibe peticiones.

---

## Práctica 8. Comprobar la sintaxis y localizar un error

**Referencia del manual:** [3.10, Cómo comprobar la sintaxis de los archivos de configuración de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-comprobar-la-sintaxis-de-los-archivos-de-configuraci%C3%B3n-de-apache).

### Objetivo de la práctica 8

Provocar un error controlado, localizarlo con `configtest` y corregirlo sin dejar Apache inutilizado.

### Pasos de la práctica 8

1. Ejecuta la comprobación con la configuración correcta:

   ```bash
   sudo apache2ctl configtest
   ```

2. Haz una copia y edita un fichero de sitio habilitado, por ejemplo:

   ```bash
   sudo cp /etc/apache2/sites-available/000-default.conf /tmp/000-default.conf.práctica8
   sudo nano /etc/apache2/sites-available/000-default.conf
   ```

3. Introduce un error sencillo y controlado, como cambiar:

   ```apache
   <VirtualHost *:80>
   ```

   por:

   ```apache
   <VirtualHost *:80
   ```

4. Ejecuta la comprobación sin reiniciar Apache:

   ```bash
   sudo apache2ctl configtest
   ```

   Guarda el mensaje de error y la línea indicada.

5. Corrige el fichero, vuelve a ejecutar `configtest` y solo cuando aparezca `Syntax OK`, recarga Apache:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl reload apache2
   ```

### Resultado esperado de la práctica 8

Mientras existe el error, `configtest` informa de un error de sintaxis. Tras corregirlo, muestra `Syntax OK`. Un error de sintaxis no debe aplicarse reiniciando el servicio.

### Recuperación de la práctica 8

Si no localizas el cambio, restaura `/tmp/000-default.conf.práctica8` y ejecuta de nuevo `configtest`.

---

## Práctica 9. Ocultar la versión de Apache

**Referencia del manual:** [3.11, Cómo ocultar la versión de Apache al cliente](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-ocultar-la-versi%C3%B3n-de-apache-al-cliente).

### Objetivo de la práctica 9

Reducir la información del servidor expuesta en las respuestas HTTP.

### Pasos de la práctica 9

1. Guarda una copia del fichero global de seguridad:

   ```bash
   sudo cp /etc/apache2/conf-available/security.conf /etc/apache2/conf-available/security.conf.bak
   ```

2. Edita `/etc/apache2/conf-available/security.conf` y deja activas estas directivas:

   ```apache
   ServerTokens Prod
   ServerSignature Off
   ```

   `ServerTokens` es una directiva global. `ServerSignature` también puede configurarse para un host virtual.

3. Comprueba y recarga Apache:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl reload apache2
   ```

4. Consulta las cabeceras que recibe el cliente:

   ```bash
   curl -I http://127.0.0.1/
   curl -Iv http://127.0.0.1/ 2>&1 | grep -i server
   ```

### Resultado esperado de la práctica 9

La cabecera `Server` no debe mostrar la versión concreta de Apache, por ejemplo `Apache/2.4.x`. Las páginas de error tampoco deben mostrar la firma completa del servidor.

### Recuperación de la práctica 9

Restaura `security.conf.bak`, comprueba la sintaxis y recarga Apache.

---

## Práctica 10. Consultar y aumentar el detalle de los logs

**Referencia del manual:** [3.12, Archivos de log de Apache](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#archivos-de-log-de-apache) y sus apartados 3.12.1, 3.12.2 y 3.12.3.

### Objetivo de la práctica 10

Localizar los logs, observar accesos correctos y errores 404, y cambiar temporalmente el nivel de detalle.

### Pasos de la práctica 10

1. Identifica los ficheros de log:

   ```bash
   sudo ls -l /var/log/apache2/
   sudo tail -n 10 /var/log/apache2/access.log
   sudo tail -n 10 /var/log/apache2/error.log
   ```

2. En una terminal del servidor, observa el registro de accesos en tiempo real:

   ```bash
   sudo tail -f /var/log/apache2/access.log
   ```

3. En otra terminal, solicita una página existente y otra inexistente:

   ```bash
   curl -I http://127.0.0.1/
   curl -I http://127.0.0.1/pagina-que-no-existe.html
   ```

   Vuelve a la terminal de `tail` y anota los códigos `200` y `404`. Pulsa `Ctrl+C` para detener el seguimiento.

4. Guarda una copia de `/etc/apache2/apache2.conf` y cambia temporalmente el nivel de log:

   ```bash
   sudo cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf.bak
   sudo nano /etc/apache2/apache2.conf
   ```

   Busca `LogLevel` o añade al final:

   ```apache
   LogLevel debug
   ```

5. Comprueba y recarga Apache:

   ```bash
   sudo apache2ctl configtest
   sudo systemctl reload apache2
   ```

6. Repite varias peticiones, consulta el log de errores y compara el detalle:

   ```bash
   curl -I http://127.0.0.1/
   curl -I http://127.0.0.1/pagina-que-no-existe.html
   sudo tail -n 30 /var/log/apache2/error.log
   ```

7. Restaura el nivel normal, que en el manual aparece como `warn`:

   ```bash
   sudo cp /etc/apache2/apache2.conf.bak /etc/apache2/apache2.conf
   sudo apache2ctl configtest
   sudo systemctl reload apache2
   ```

### Resultado esperado de la práctica 10

`access.log` registra las peticiones recibidas, incluidas las respuestas `200` y `404`. `error.log` registra los errores del servidor. Con `LogLevel debug` se obtiene mucho más detalle que con `warn`.

### Precaución de la práctica 10

No mantengas `LogLevel debug` en un servidor de producción: genera mucho volumen de información y puede dificultar la lectura de los problemas reales.

### Entrega de la práctica 10

Incluye un fragmento de cada log y explica que peticion produjo cada linea.

---

## Entrega final

Crea un fichero Markdown con una sección para cada práctica. En cada sección incluye:

- comandos ejecutados;
- ficheros de configuración modificados;
- capturas o salidas que demuestren el resultado;
- errores encontrados y su solución;
- forma de restaurar el estado inicial.
