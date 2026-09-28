# Práctica: Instalación de un Servidor Web LAMP en Debian 13

---

**Objetivo:** El objetivo de esta práctica es instalar y configurar un entorno de servidor web completo (Apache, MariaDB y PHP) en una máquina virtual Debian 13 sin entorno gráfico, preparándola para alojar aplicaciones web. Se reforzará el uso de la línea de comandos, la gestión de servicios y la configuración de red en un entorno virtualizado.

---

## 1. Creación de la Máquina Virtual Debian 13

El primer paso es crear la máquina virtual (MV) en VirtualBox donde se alojará nuestro servidor.

1. **Descargar la imagen de Debian:** descarga la imagen de instalación “netinst” de Debian 13 “Trixie” desde el [sitio web oficial de Debian](https://www.debian.org/devel/debian-installer/). Esta versión es mínima e ideal para servidores, ya que descarga los paquetes necesarios durante la instalación.
2. **Crear la nueva máquina virtual en VirtualBox:**
   - Haz clic en “Nueva”.
   - **Nombre:** `debian13`
   - **Tipo:** `Linux`
   - **Versión:** `Debian (64-bit)`
   - **Memoria RAM:** asigna al menos 1024 MB, aunque 2048 MB es recomendable. 2048 suele ser el valor por defecto.
   - **Disco duro:** crea un disco duro virtual ahora. Elige VDI, reservado dinámicamente, y asígnale un tamaño de 15 GB o 20 GB. 20 GB suele ser el valor por defecto.
3. **Configurar la red de la MV:**
   - Selecciona la MV recién creada y ve a “Configuración” > “Red”.
   - **Adaptador 1:** asegúrate de que esté conectado a “NAT”. Esta es la configuración por defecto y permitirá a la MV tener acceso a internet a través de tu ordenador.
4. **Instalación de Debian 13:**
   - Inicia la MV. Te pedirá que selecciones un disco de inicio. Elige la imagen `.iso` de Debian que descargaste.
   - Procede con la instalación en modo “Install” (no “Graphical Install”).
   - **Configuración:** sigue los pasos del instalador: idioma, ubicación, teclado, etc.
   - **Nombre de la máquina:** `debian-server` (por ejemplo).
   - **Nombre de dominio:** déjalo en blanco.
   - **Contraseña de root:** establece una contraseña segura y recuérdala.
   - **Crear un usuario:** crea un usuario no-root (por ejemplo, `usuario`) y asígnale una contraseña.
   - **Particionado de discos:** para esta práctica, puedes utilizar el método “Guiado - utilizar todo el disco”.
   - **Selección de software:** en el paso “Software selection”, es muy importante desmarcar todas las opciones, especialmente “Debian desktop environment”. Solo debemos dejar marcadas:
     - **SSH server**
     - **standard system utilities**
   - Finaliza la instalación y reinicia la MV. VirtualBox debería expulsar la ISO automáticamente.

---

## 2. Habilitar y Configurar la Conexión SSH

Hemos preinstalado el servidor SSH durante la instalación de Debian. Ahora vamos a configurar el reenvío de puertos en VirtualBox para poder conectarnos desde nuestro equipo anfitrión (tu ordenador) a la MV.

### 2.1 Configurar el reenvío de puertos en VirtualBox

Con la MV apagada:

1. Ve a “Configuración” > “Red” > “Adaptador 1”.
2. Despliega la sección “Avanzadas”.
3. Haz clic en el botón “Reenvío de puertos”.
4. Añade una nueva regla con los siguientes datos:
   - **Nombre:** `SSH`
   - **Protocolo:** `TCP`
   - **IP anfitrión:** déjalo en blanco (o `127.0.0.1`).
   - **Puerto anfitrión:** `2222` (este es el puerto que usaremos en nuestro PC para conectar; usamos uno alto para evitar conflictos).
   - **IP invitado:** déjalo en blanco (VirtualBox lo gestionará). También puedes poner la IP de la máquina virtual, que normalmente es `10.0.15.2`.
   - **Puerto invitado:** `22` (este es el puerto estándar de SSH donde escucha el servidor en la MV).

### 2.2 Conectarse por SSH

1. Inicia tu máquina virtual Debian 13. No necesitas hacer login en la consola de VirtualBox, solo asegúrate de que esté en funcionamiento.
2. Abre un terminal o cliente SSH en tu máquina anfitriona:
   - **En Windows:** puedes usar PowerShell, CMD o un cliente como PuTTY.
   - **En Linux/macOS:** usa la terminal.
3. Ejecuta el siguiente comando para conectarte. Sustituye `usuario` por el nombre de usuario que creaste durante la instalación:

```bash
ssh usuario@127.0.0.1 -p 2222
```

   - `ssh`: el comando para iniciar la conexión.
   - `usuario@127.0.0.1`: conecta como el usuario indicado al localhost (nuestro propio equipo).
   - `-p 2222`: especifica que la conexión debe hacerse a través del puerto 2222 de nuestro anfitrión. VirtualBox redirigirá este tráfico al puerto 22 de la MV.

4. La primera vez te preguntará si confía en la huella digital del servidor. Escribe `yes`.
5. Introduce la contraseña de tu usuario en la MV. ¡Ya estás dentro! A partir de ahora, realizaremos toda la configuración a través de esta terminal SSH.

---

## 3. Actualización del sistema

Antes de instalar nuevo software, es una buena práctica actualizar la lista de paquetes y el sistema operativo.

```bash
sudo apt update && sudo apt upgrade -y
```

- `sudo`: ejecuta el comando con privilegios de superusuario (root). Te pedirá la contraseña de tu usuario.
- `apt update`: descarga la información más reciente sobre los paquetes disponibles desde los repositorios de Debian.
- `&&`: este operador encadena comandos. El segundo solo se ejecuta si el primero tiene éxito.
- `apt upgrade -y`: actualiza todos los paquetes instalados a sus últimas versiones. La opción `-y` responde afirmativamente a cualquier pregunta, automatizando el proceso.

---

## 4. Instalación del servidor web Apache2

Apache es uno de los servidores web más populares del mundo.

### 4.1 Instalación

```bash
sudo apt install apache2 -y
```

El gestor de paquetes `apt` descargará e instalará Apache y todas sus dependencias.

### 4.2 Comprobación del servicio

Una vez instalado, el servicio de Apache se inicia automáticamente. Podemos comprobar su estado:

```bash
sudo systemctl status apache2
```

Deberías ver una salida en verde que indique `active (running)`. Si no es así, puedes iniciarlo manualmente con `sudo systemctl start apache2`. Para salir de la vista de estado, pulsa la tecla `q`.

---

## 5. Instalación del gestor de base de datos MariaDB

MariaDB es un sistema de gestión de bases de datos relacional, un fork de MySQL mantenido por la comunidad.

### 5.1 Instalación

```bash
sudo apt install mariadb-server -y
```

### 5.2 Script de securización

Por defecto, la instalación de MariaDB no es del todo segura. Debian proporciona un script para mejorar la seguridad inicial.

```bash
sudo mariadb-secure-installation
```

El script te hará varias preguntas. Aquí tienes las respuestas recomendadas:

- **Enter current password for root (enter for none):** pulsa `Enter` (la primera vez no hay contraseña).
- **Switch to unix_socket authentication? [Y/n]:** escribe `n`. Esto nos permitirá establecer una contraseña de root más adelante si lo necesitamos.
- **Change the root password? [Y/n]:** escribe `Y` y establece una contraseña segura para el usuario root de la base de datos. Utiliza algo que recuerdes, por ejemplo `root`.
- **Remove anonymous users? [Y/n]:** escribe `Y`.
- **Disallow root login remotely? [Y/n]:** escribe `Y`.
- **Remove test database and access to it? [Y/n]:** escribe `Y`.
- **Reload privilege tables now? [Y/n]:** escribe `Y`.

Con esto, nuestra base de datos es mucho más segura.

---

## 6. Instalación de PHP en modo FPM

PHP _Hypertext Preprocessor_ es el lenguaje de programación del lado del servidor que usaremos. FPM (FastCGI Process Manager) es una implementación avanzada de FastCGI que ofrece un mejor rendimiento que el módulo tradicional de Apache `mod_php`.

### 6.1 Instalación de PHP-FPM y módulos comunes

Instalaremos PHP-FPM junto con el módulo para conectar con MariaDB/MySQL.

```bash
sudo apt install php-fpm php-mysql -y
```

- `php-fpm`: el gestor de procesos FPM.
- `php-mysql`: la extensión que permite a PHP comunicarse con bases de datos MariaDB y MySQL.

### 6.2 Configuración de Apache para usar PHP-FPM

Para que Apache pueda procesar archivos PHP a través de FPM, necesitamos habilitar algunos módulos y una configuración específica.

1. **Habilitar los módulos necesarios:**

```bash
sudo a2enmod proxy_fcgi setenvif
```

- `a2enmod`: utilidad de Apache 2 para habilitar módulos.
- `proxy_fcgi`: permite a Apache redirigir peticiones a un servidor FastCGI externo (en este caso, PHP-FPM).
- `setenvif`: necesario para la configuración de `proxy_fcgi`.

2. **Habilitar la configuración de PHP-FPM:**

```bash
sudo a2enconf php8.4-fpm
```

- `a2enconf`: utilidad similar a la anterior, pero para archivos de configuración.
- `php8.4-fpm`: activa el archivo de configuración que conecta Apache con el socket de PHP-FPM. La versión puede variar; usa la que se haya instalado en tu sistema. Puedes comprobarlo con `ls /etc/apache2/conf-available/`.

3. **Reiniciar Apache para aplicar los cambios:** cada vez que modificamos la configuración de Apache, debemos reiniciar el servicio.

```bash
sudo systemctl restart apache2
```

---

## 7. Comprobaciones finales

Vamos a verificar que todos los componentes funcionen correctamente.

### 7.1 Comprobar Apache

El servidor web de Apache sirve archivos desde el directorio `/var/www/html`. Por defecto, contiene una página de bienvenida. Para verla, necesitamos hacer un reenvío de puertos para el tráfico web (HTTP), igual que hicimos para SSH.

1. **Apaga la MV.**
2. Ve a “Configuración” > “Red” > “Avanzadas” > “Reenvío de puertos”.
3. Añade una nueva regla:
   - **Nombre:** `HTTP-Apache`
   - **Protocolo:** `TCP`
   - **Puerto anfitrión:** `8080`
   - **Puerto invitado:** `80`
4. Inicia la MV.
5. Abre un navegador web en tu ordenador anfitrión y visita la dirección: `http://127.0.0.1:8080`.

Deberías ver la página por defecto de Apache2 para Debian, que dice “It works!”.

### 7.2 Comprobar MariaDB

Verificamos que el servicio de la base de datos está activo.

```bash
sudo systemctl status mariadb
```

Deberías ver una salida `active (running)`. Pulsa `q` para salir.

### 7.3 Comprobar la ejecución de PHP

Vamos a crear un archivo PHP que nos mostrará toda la información de configuración de PHP.

1. Crea un archivo de prueba PHP con el editor `nano`:

```bash
sudo nano /var/www/html/info.php
```

1. Pega el siguiente código en el editor:

```php
<?php
phpinfo();
?>
```

1. Guarda y cierra el archivo:
   - `Ctrl + O` para guardar.
   - `Enter` para confirmar el nombre del archivo.
   - `Ctrl + X` para salir de `nano`.

1. Verifica desde el navegador:
   - Abre tu navegador y visita `http://127.0.0.1:8080/info.php`.
   - Deberías ver una larga página con el logo de PHP y mucha información sobre su configuración. Esto confirma que Apache está procesando archivos PHP correctamente.

### 7.4 Comprobar la ejecución de PHP junto con MariaDB

Vamos a crear un nuevo archivo PHP que intentará conectarse con la base de datos.

1. Crea un archivo de prueba PHP:

```bash
sudo nano /var/www/html/mariadb.php
```

2. Pega el siguiente código en el editor:

```php
<?php

echo "<h1>Prueba de conexión a MariaDB</h1>";
$servidor = "localhost";
$usuario_db = "root";
$contrasena_db = "tu_contraseña_segura"; // <-- ¡CAMBIA ESTO!
$nombre_db = "mysql"; // Usamos la base de datos 'mysql' que siempre existe

try {
    $conn = new PDO("mysql:host=$servidor;dbname=$nombre_db", $usuario_db, $contrasena_db);
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    echo "<p style='color:green;'>¡Conexión a MariaDB exitosa!</p>";
} catch (PDOException $e) {
    echo "<p style='color:red;'>Error en la conexión: " . $e->getMessage() . "</p>";
}

$conn = null;
?>
```

> **Importante:** reemplaza `tu_contraseña_segura` con la contraseña de root de MariaDB que estableciste durante el script `mysql_secure_installation`.

3. Guarda y cierra el archivo:
   - `Ctrl + O` para guardar.
   - `Enter` para confirmar el nombre del archivo.
   - `Ctrl + X` para salir de `nano`.
4. Verifica desde el navegador:
   - Abre tu navegador y visita `http://127.0.0.1:8080/mariadb.php`.
   - Deberías ver el mensaje: **“¡Conexión a MariaDB exitosa!”** en color verde. Esto confirma que PHP puede comunicarse con la base de datos.

**¡Enhorabuena! Has instalado y configurado correctamente un servidor web LAMP en Debian 13.**
