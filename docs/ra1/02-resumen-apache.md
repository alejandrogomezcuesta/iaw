# 1. Introducción a Apache HTTP Server

Apache HTTP Server, conocido habitualmente como Apache, es un servidor web de código abierto. Recibe peticiones HTTP de los clientes, busca el contenido solicitado y devuelve una respuesta. En Debian y Ubuntu, el contenido web predeterminado se encuentra en `/var/www/html` y el servicio se llama `apache2`.

Esta introducción se centra en la organización utilizada por Debian y Ubuntu. En otras distribuciones pueden cambiar los nombres y las rutas de los archivos.

## 2. Instalación y comprobación

Instala Apache desde los repositorios de la distribución:

```bash
sudo apt update
sudo apt install apache2
```

Comprueba el estado del servicio:

```bash
sudo systemctl status apache2
```

Si está funcionando, el estado indica `active (running)`. También puedes hacer una petición desde el propio servidor:

```bash
curl -I http://127.0.0.1/
```

Una respuesta como `HTTP/1.1 200 OK` confirma que Apache ha atendido la petición. Desde un navegador también se puede visitar `http://<IP_DEL_SERVIDOR>/`; normalmente aparecerá la página inicial de Apache.

## 3. Directorio de configuración

Los archivos de configuración de Apache se encuentran en `/etc/apache2`. Este es un listado representativo; los contenidos pueden variar según la versión y los paquetes instalados:

```text
/etc/apache2/
├── apache2.conf
├── ports.conf
├── envvars
├── magic
├── conf-available/
├── conf-enabled/
├── mods-available/
├── mods-enabled/
├── sites-available/
└── sites-enabled/
```

Puedes consultar el listado de tu sistema con:

```bash
ls -la /etc/apache2/
```

Los directorios terminados en `available` guardan elementos disponibles para activar. Los terminados en `enabled` contienen normalmente enlaces simbólicos a los elementos que Apache debe cargar. Por tanto, tener un archivo en `available` no implica que esté activo.

## 4. Archivos principales

### 4.1. `apache2.conf`

Es el archivo principal de configuración. Define opciones generales del servidor e incorpora los archivos habilitados, entre ellos los fragmentos de `conf-enabled`, los módulos de `mods-enabled` y los sitios de `sites-enabled`. También incorpora `ports.conf`.

### 4.2. `ports.conf`

Indica en qué puertos escucha Apache mediante directivas `Listen`. En una instalación habitual, HTTP utiliza el puerto TCP `80` y HTTPS el `443`. Si se cambia el puerto de un sitio virtual, la escucha definida aquí debe ser coherente con el puerto del bloque `<VirtualHost>` correspondiente.

## 5. Configuraciones, módulos y sitios virtuales

Apache organiza estos tres tipos de elementos por separado. En todos los casos, `*-available` reúne los elementos disponibles y `*-enabled` señala los que se incorporan a la configuración activa, normalmente mediante enlaces simbólicos.

| Tipo | Directorios | Para qué sirve |
| --- | --- | --- |
| Configuración global | `conf-available/` y `conf-enabled/` | Guarda fragmentos con directivas que se aplican al servidor en general o a una función compartida. |
| Módulos | `mods-available/` y `mods-enabled/` | Guarda los módulos instalados y sus ajustes; los módulos habilitados añaden funciones a Apache. |
| Sitios virtuales | `sites-available/` y `sites-enabled/` | Guarda las definiciones de los sitios web que Apache puede servir; cada sitio puede tener su propio nombre, directorio y opciones. |

En Debian y Ubuntu se utilizan estas órdenes para habilitar o deshabilitar cada tipo de elemento:

| Elemento | Habilitar | Deshabilitar |
| --- | --- | --- |
| Configuración | `a2enconf` | `a2disconf` |
| Módulo | `a2enmod` | `a2dismod` |
| Sitio virtual | `a2ensite` | `a2dissite` |

Por ejemplo, `sudo a2ensite ejemplo.conf` habilita un sitio que ya está definido en `sites-available`. Estas herramientas gestionan los enlaces entre `available` y `enabled`; no crean por sí mismas el contenido del módulo o del sitio. Después de cambiar la configuración, se puede validar con `sudo apache2ctl configtest` y aplicar los cambios con `sudo systemctl reload apache2`.

## 6. Ejemplos de archivos

### 6.1. Fragmento de configuración

Un archivo como `/etc/apache2/conf-available/ejemplo.conf` puede contener directivas generales. Este ejemplo reduce la información de versión que Apache comunica:

```apache
ServerTokens Prod
ServerSignature Off
```

Al habilitar el fragmento, Apache lo incorpora desde `conf-enabled`.

Habilítalo o deshabilítalo con el nombre del fragmento, sin la extensión `.conf`:

```bash
sudo a2enconf ejemplo
sudo a2disconf ejemplo
```

`a2enconf` crea el enlace en `conf-enabled`; `a2disconf` lo quita. En ambos casos, el archivo original permanece en `conf-available`.

### 6.2. Módulo

Los archivos `.load` indican qué módulo debe cargar Apache. Por ejemplo, un archivo como `status.load` puede incluir una directiva de esta forma:

```apache
LoadModule status_module /usr/lib/apache2/modules/mod_status.so
```

Algunos módulos también tienen un archivo `.conf` asociado con sus directivas de configuración. `a2enmod status` habilita los archivos correspondientes en `mods-enabled`.

Para activar o desactivar este módulo:

```bash
sudo a2enmod status
sudo a2dismod status
```

`a2enmod` crea los enlaces del módulo en `mods-enabled`; `a2dismod` los quita, pero no desinstala el módulo ni borra sus archivos de `mods-available`.

### 6.3. Sitio virtual

Un archivo como `/etc/apache2/sites-available/ejemplo.conf` puede definir un sitio web para el puerto 80:

```apache
<VirtualHost *:80>
    ServerName ejemplo.test
    DocumentRoot /var/www/ejemplo

    <IfModule mod_rewrite.c>
        RewriteEngine On
        RewriteRule ^/inicio$ /index.html [R=302,L]
    </IfModule>
</VirtualHost>
```

`ServerName` identifica el nombre del sitio y `DocumentRoot` indica desde qué directorio se sirven sus archivos. El bloque `<IfModule mod_rewrite.c>` aplica sus directivas solo si el módulo `mod_rewrite` está cargado; en este ejemplo, `/inicio` redirige a `/index.html`. `a2ensite ejemplo.conf` habilita el sitio mediante un enlace en `sites-enabled`.

Activa o desactiva el sitio virtual con su nombre de archivo:

```bash
sudo a2ensite ejemplo.conf
sudo a2dissite ejemplo.conf
```

`a2ensite` crea el enlace en `sites-enabled`; `a2dissite` lo quita, pero conserva la definición en `sites-available`.

Después de habilitar o deshabilitar elementos, comprueba la sintaxis y recarga Apache para aplicar el cambio:

```bash
sudo apache2ctl configtest
sudo systemctl reload apache2
```

Recarga el servicio solo si la comprobación termina con `Syntax OK`.

## 7. Fuente

Resumen basado en [Introducción a Apache HTTP Server](https://elpuig.xeill.net/Members/vcarceler/articulos/introduccion-a-apache-http-server), de El Puig.
