# Prácticas guiadas con MariaDB

## Objetivo y condiciones de trabajo

Estas prácticas convierten los ejercicios del documento **Ejercicios con MariaDB** en un recorrido guiado basado en los apartados 4.1 a 4.11 del manual [**LAMP Stack en Ubuntu Server**](https://josejuansanchez.org/iaw/practica-01-01-teoria/).

Trabaja sobre una máquina virtual Ubuntu Server con MariaDB instalado. No es necesario crear una instantánea antes de cada práctica. Si lo necesitas, puedes crear una instantánea de la máquina virtual al terminar todas las prácticas.

Todos los comandos se ejecutan en el servidor salvo que se indique **CLIENTE**. Sustituye los valores entre `< >` por los datos de tu entorno. En el manual se utiliza a menudo el nombre MySQL Server, pero las órdenes están adaptadas a MariaDB.

No utilices contraseñas reales en los ejemplos ni compartas las contraseñas de `root`. Para las pruebas, usa una contraseña temporal y segura.

---

## Práctica 1. Instalar y asegurar MariaDB

**Referencia del manual:** [4.1 Instalación de MySQL Server](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#instalaci%C3%B3n-de-mysql-server), [4.2 Cómo iniciar, parar y consultar el estado de MySQL Server](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-iniciar-parar-y-consultar-el-estado-de-mysql-server), [4.3 Archivos de configuración de MySQL Server](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#archivos-de-configuraci%C3%B3n-de-mysql-server), [4.4 Archivos de log de MySQL Server](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#archivos-de-log-de-mysql-server) y [4.5 Cómo acceder a MySQL Server desde consola con el usuario `root`](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-acceder-a-mysql-server-desde-consola-con-el-usuario-root).

### Objetivo de la práctica 1

Instalar MariaDB, comprobar el estado del servicio, localizar sus ficheros principales y ejecutar el asistente de seguridad inicial.

### Pasos de la práctica 1

1. Actualiza los repositorios e instala MariaDB:

   ```bash
   # Actualiza el índice local de paquetes disponibles.
   sudo apt update
   # Instala el servidor y el cliente de MariaDB.
   sudo apt install mariadb-server mariadb-client -y
   ```

2. Comprueba que el servicio está activo y habilitado para iniciarse con el sistema:

   ```bash
   # Muestra si el servicio MariaDB está activo y sus últimos mensajes.
   sudo systemctl status mariadb
   # Comprueba si MariaDB está configurado para iniciarse con el sistema.
   sudo systemctl is-enabled mariadb
   ```

   Si no estuviera iniciado, arráncalo y vuelve a consultar su estado:

   ```bash
   # Inicia el servicio MariaDB.
   sudo systemctl start mariadb
   # Confirma que MariaDB ha quedado activo.
   sudo systemctl status mariadb
   ```

3. Ejecuta el asistente de seguridad:

   ```bash
   # Ejecuta el asistente interactivo de seguridad inicial de MariaDB.
   sudo mariadb-secure-installation
   ```

   Responde a las preguntas de forma segura. En una instalación nueva, cuando pregunte por la contraseña actual de `root`, pulsa **Enter** si no existe ninguna. Elige eliminar usuarios anónimos, borrar la base de datos de prueba, deshabilitar el acceso remoto de `root` y recargar las tablas de privilegios. Si necesitas autenticar `root` mediante contraseña, establece una contraseña temporal segura cuando el asistente lo solicite.

4. Accede a la consola de MariaDB como `root` mediante la autenticación local del sistema:

   ```bash
   # Abre la consola local de MariaDB con privilegios de administrador.
   sudo mariadb
   ```

   Comprueba el servidor y sal de la consola:

   ```sql
   -- Muestra la versión del servidor MariaDB.
   SELECT VERSION();
   -- Lista las bases de datos disponibles.
   SHOW DATABASES;
   -- Cierra la consola de MariaDB.
   EXIT;
   ```

5. Localiza los ficheros de configuración y el registro de errores:

   ```bash
   # Lista los archivos y directorios principales de configuración de MySQL/MariaDB.
   sudo ls -l /etc/mysql/
   # Lista los directorios de configuración adicional del servidor.
   sudo ls -l /etc/mysql/conf.d/ /etc/mysql/mariadb.conf.d/
   # Lista los archivos de registro disponibles.
   sudo ls -l /var/log/mysql/
   # Muestra las veinte líneas más recientes del registro de errores.
   sudo tail -n 20 /var/log/mysql/error.log
   ```

   En algunas instalaciones el fichero o directorio de log puede no existir hasta que se produzca un evento registrado. No lo crees manualmente: comprueba primero la configuración efectiva del servidor.

### Resultado esperado de la práctica 1

El servicio `mariadb` aparece activo, se puede entrar con `sudo mariadb`, se muestran las bases de datos del sistema y el asistente de seguridad ha eliminado las opciones inseguras seleccionadas.

#### Captura de pantalla de la práctica 1

Envía una sola captura de la terminal donde se vea que el servicio MariaDB está activo (`active (running)`) y una consulta correcta de la versión del servidor.

### Recuperación de la práctica 1

Para repetir el asistente, ejecútalo de nuevo con `sudo mariadb-secure-installation`. Para detener o iniciar el servicio utiliza `sudo systemctl stop mariadb` y `sudo systemctl start mariadb`. No desinstales MariaDB si todavía necesitas las bases de datos creadas en las prácticas siguientes.

---

## Práctica 2. Consultar, crear y borrar bases de datos

**Referencia del manual:** [4.5 Cómo acceder a MySQL Server desde consola con el usuario `root`](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-acceder-a-mysql-server-desde-consola-con-el-usuario-root) y [4.8 Algunos comandos útiles para MySQL Server desde consola](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#algunos-comandos-%C3%BAtiles-para-mysql-server-desde-consola).

### Objetivo de la práctica 2

Consultar las bases de datos existentes, crear una base de datos, seleccionar una base de datos, listar sus tablas, consultar la estructura de una tabla y borrar la base de datos.

### Pasos de la práctica 2

1. Abre la consola de MariaDB como `root` y consulta las bases de datos existentes:

   ```bash
   # Abre la consola local de MariaDB con privilegios de administrador.
   sudo mariadb
   ```

   ```sql
   -- Lista las bases de datos existentes.
   SHOW DATABASES;
   ```

2. Crea una base de datos de pruebas y selecciónala:

   ```sql
   -- Crea la base de datos temporal de la práctica.
   CREATE DATABASE practica_mariadb;
   -- Selecciona la base de datos para las siguientes sentencias.
   USE practica_mariadb;
   ```

3. Crea una tabla de ejemplo y comprueba que aparece en la base de datos:

   ```sql
      -- Crea una tabla con identificador, nombre y correo de cada cliente.
   CREATE TABLE clientes (
       id INT AUTO_INCREMENT PRIMARY KEY,
       nombre VARCHAR(100) NOT NULL,
       correo VARCHAR(150) NOT NULL
   );

      -- Muestra las tablas de la base de datos seleccionada.
   SHOW TABLES;
   ```

4. Consulta la estructura de la tabla concreta:

   ```sql
   -- Describe las columnas, tipos y claves de la tabla clientes.
   DESCRIBE clientes;
   ```

   También puedes utilizar esta forma equivalente:

   ```sql
   -- Muestra las columnas de clientes; es equivalente a DESCRIBE.
   SHOW COLUMNS FROM clientes;
   ```

5. Sal de la consola y vuelve a entrar para comprobar que la base de datos permanece creada:

   ```sql
   -- Cierra la consola de MariaDB.
   EXIT;
   ```

   ```bash
   # Lista las bases de datos ejecutando una consulta sin abrir la consola interactiva.
   sudo mariadb -e "SHOW DATABASES;"
   ```

6. Cuando hayas terminado las comprobaciones, borra la base de datos de pruebas:

   ```bash
   # Abre la consola local de MariaDB con privilegios de administrador.
   sudo mariadb
   ```

   ```sql
   -- Elimina la base de datos temporal y todas sus tablas y datos.
   DROP DATABASE practica_mariadb;
   -- Comprueba que la base de datos ya no aparece.
   SHOW DATABASES;
   -- Cierra la consola de MariaDB.
   EXIT;
   ```

### Resultado esperado de la práctica 2

`SHOW DATABASES` muestra `practica_mariadb` después de crearla. Tras seleccionar la base de datos, `SHOW TABLES` muestra `clientes` y `DESCRIBE clientes` muestra sus columnas. Después de `DROP DATABASE`, la base de datos deja de aparecer.

#### Captura de pantalla de la práctica 2

Antes de borrar la base de datos, envía una sola captura de la consola de MariaDB donde se vean `practica_mariadb`, la tabla `clientes` y la salida de `DESCRIBE clientes`.

### Recuperación de la práctica 2

Si la base de datos de pruebas ya no es necesaria, ejecuta `DROP DATABASE practica_mariadb;`. No ejecutes esta orden sobre una base de datos real: elimina todas sus tablas y datos de forma irreversible.

---

## Práctica 3. Crear usuarios y administrar permisos

**Referencia del manual:** [4.9 Usuarios y permisos en MySQL Server desde consola](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#usuarios-y-permisos-en-mysql-server-desde-consola), [4.9.1 Eliminar un usuario](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#eliminar-un-usuario), [4.9.2 Crear un nuevo usuario](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#crear-un-nuevo-usuario), [4.9.4 Asignar permisos a un usuario](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#asignar-permisos-a-un-usuario), [4.9.5 Eliminar permisos a un usuario](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#eliminar-permisos-a-un-usuario) y [4.9.6 Consultar los usuarios creados en MySQL Server](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#consultar-los-usuarios-creados-en-mysql-server).

### Objetivo de la práctica 3

Crear un usuario asociado a un origen concreto, concederle todos los permisos sobre una base de datos, revocar esos permisos, permitir el acceso desde otro origen y borrar el usuario.

### Pasos de la práctica 3

1. Crea de nuevo la base de datos de pruebas y dos cuentas con el mismo nombre, pero con orígenes distintos. Sustituye `<IP_CLIENTE>` por la IP real del equipo cliente que utilizarás:

   ```bash
   # Abre la consola local de MariaDB con privilegios de administrador.
   sudo mariadb
   ```

   ```sql
   -- Crea la base de datos que se utilizará para probar permisos.
   CREATE DATABASE practica_mariadb;
   -- Crea una cuenta alumno que solo puede autenticarse desde el propio servidor.
   CREATE USER 'alumno'@'localhost' IDENTIFIED BY '<CONTRASEÑA_TEMPORAL>';
   -- Crea otra cuenta alumno limitada a la IP indicada del cliente.
   CREATE USER 'alumno'@'<IP_CLIENTE>' IDENTIFIED BY '<CONTRASEÑA_TEMPORAL>';
   ```

   En MariaDB, `'alumno'@'localhost'` y `'alumno'@'<IP_CLIENTE>'` son cuentas diferentes. El campo `Host` forma parte de la identidad de la cuenta.

2. Comprueba los usuarios y los orígenes desde los que pueden conectarse:

   ```sql
   -- Lista las cuentas alumno y el origen desde el que puede conectarse cada una.
   SELECT User, Host FROM mysql.user WHERE User = 'alumno';
   ```

3. Concede todos los permisos únicamente sobre la base de datos de pruebas a la cuenta local:

   ```sql
   -- Concede todos los privilegios sobre practica_mariadb a la cuenta local.
   GRANT ALL PRIVILEGES ON practica_mariadb.* TO 'alumno'@'localhost';
   -- Recarga las tablas de privilegios del servidor.
   FLUSH PRIVILEGES;
   -- Muestra los privilegios asignados a la cuenta local.
   SHOW GRANTS FOR 'alumno'@'localhost';
   ```

4. Prueba la conexión local con el usuario creado:

   ```bash
   # Inicia sesión en practica_mariadb como alumno y solicita la contraseña.
   mariadb -u alumno -p practica_mariadb
   ```

   Introduce la contraseña temporal y comprueba el contexto:

   ```sql
   -- Muestra la cuenta efectiva y la base de datos seleccionada.
   SELECT CURRENT_USER(), DATABASE();
   -- Cierra la sesión de MariaDB.
   EXIT;
   ```

5. Revoca los permisos sobre la base de datos y comprueba las concesiones:

   ```bash
   # Abre la consola local de MariaDB con privilegios de administrador.
   sudo mariadb
   ```

   ```sql
   -- Revoca los privilegios de la cuenta local sobre la base de pruebas.
   REVOKE ALL PRIVILEGES ON practica_mariadb.* FROM 'alumno'@'localhost';
   -- Recarga las tablas de privilegios del servidor.
   FLUSH PRIVILEGES;
   -- Verifica los privilegios que conserva la cuenta local.
   SHOW GRANTS FOR 'alumno'@'localhost';
   ```

6. Para comprobar el acceso desde una IP concreta, asigna permisos a la segunda cuenta. Hazlo solo en una red de prácticas y limita `<IP_CLIENTE>` a una dirección conocida; no utilices `'%'` salvo que el ejercicio requiera explícitamente cualquier origen:

   ```sql
   -- Concede permisos sobre la base de pruebas a la cuenta del cliente indicado.
   GRANT ALL PRIVILEGES ON practica_mariadb.* TO 'alumno'@'<IP_CLIENTE>';
   -- Recarga las tablas de privilegios del servidor.
   FLUSH PRIVILEGES;
   -- Verifica los permisos concedidos a la cuenta remota.
   SHOW GRANTS FOR 'alumno'@'<IP_CLIENTE>';
   ```

   Desde el **CLIENTE**, prueba la conexión indicando la IP real del servidor en lugar de `192.0.2.10`:

   ```bash
   # Conecta con MariaDB en el servidor remoto y solicita la contraseña de alumno.
   mariadb -h 192.0.2.10 -u alumno -p practica_mariadb
   ```

7. Elimina las dos cuentas y la base de datos de pruebas:

   ```sql
   -- Elimina la cuenta de prueba que solo accede desde el servidor local.
   DROP USER IF EXISTS 'alumno'@'localhost';
   -- Elimina la cuenta de prueba asociada a la IP del cliente.
   DROP USER IF EXISTS 'alumno'@'<IP_CLIENTE>';
   -- Elimina la base de pruebas y todas sus tablas y datos.
   DROP DATABASE IF EXISTS practica_mariadb;
   -- Recarga las tablas de privilegios del servidor.
   FLUSH PRIVILEGES;
   -- Comprueba que no quedan cuentas alumno.
   SELECT User, Host FROM mysql.user WHERE User = 'alumno';
   -- Cierra la consola de MariaDB.
   EXIT;
   ```

### Resultado esperado de la práctica 3

La consulta sobre `mysql.user` muestra dos filas para `alumno`, cada una con un `Host` distinto. `SHOW GRANTS` confirma los permisos concedidos y, después de `REVOKE`, ya no aparecen los permisos sobre `practica_mariadb`. Finalmente, las cuentas y la base de datos dejan de existir.

#### Captura de pantalla de la práctica 3

Envía una sola captura de la terminal del cliente donde se vea la conexión realizada con la cuenta asociada a `<IP_CLIENTE>` y el resultado de `SELECT CURRENT_USER(), DATABASE();`.

### Recuperación de la práctica 3

Elimina únicamente las cuentas de prueba con `DROP USER IF EXISTS` y borra `practica_mariadb` si ya no la necesitas. No modifiques ni elimines las cuentas internas de MariaDB, como `root`, `mysql`, `mariadb.sys` o las cuentas de mantenimiento del sistema.

---

## Práctica 4. Ejecutar scripts SQL y sentencias desde Bash

**Referencia del manual:** [4.10 Cómo ejecutar un script `.sql` desde la consola](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-ejecutar-un-script-.sql-desde-la-consola) y [4.11 Cómo ejecutar sentencias SQL desde un script de Bash](https://josejuansanchez.org/iaw/practica-01-01-teoria/index.html#c%C3%B3mo-ejecutar-sentencias-sql-desde-un-script-de-bash).

### Objetivo de la práctica 4

Crear dos scripts que consulten las bases de datos y los usuarios de MariaDB, ejecutarlos desde la consola y automatizar las mismas consultas mediante un script de Bash.

### Pasos de la práctica 4

1. Crea `listar-bases.sql` con una consulta de las bases de datos instaladas:

   ```sql
   -- Lista las bases de datos disponibles en el servidor.
   SHOW DATABASES;
   ```

2. Ejecuta el script desde la consola de Linux:

   ```bash
   # Ejecuta el archivo listar-bases.sql con privilegios de administrador.
   sudo mariadb < listar-bases.sql
   ```

   También puedes abrir una consola de MariaDB y ejecutarlo desde ella:

   ```bash
   # Abre la consola interactiva de MariaDB.
   sudo mariadb
   ```

   ```sql
   -- Ejecuta dentro de MariaDB las sentencias del archivo indicado.
   SOURCE listar-bases.sql;
   -- Cierra la consola de MariaDB.
   EXIT;
   ```

3. Crea `listar-usuarios.sql` para mostrar los nombres de usuario y sus orígenes:

   ```sql
   -- Lista las cuentas y los orígenes desde los que pueden conectarse.
   SELECT User, Host FROM mysql.user ORDER BY User, Host;
   ```

4. Ejecuta el segundo script:

   ```bash
   # Ejecuta listar-usuarios.sql con privilegios de administrador.
   sudo mariadb < listar-usuarios.sql
   ```

5. Crea `consultas-mariadb.sh` con dos sentencias ejecutadas mediante la opción `-e`:

   ```bash
   #!/usr/bin/env bash
   # Detiene el script si falla una orden, falta una variable o falla una tubería.
   set -euo pipefail

   # Anuncia que se mostrarán las bases de datos disponibles.
   echo "Bases de datos disponibles:"
   # Lista las bases de datos sin encabezados ni formato de tabla.
   sudo mariadb --batch --skip-column-names -e "SHOW DATABASES;"

   # Anuncia que se mostrarán las cuentas y sus orígenes.
   echo "Usuarios y orígenes permitidos:"
   # Lista los usuarios y hosts ordenados, sin encabezados ni formato de tabla.
   sudo mariadb --batch --skip-column-names -e "SELECT User, Host FROM mysql.user ORDER BY User, Host;"
   ```

6. Dale permiso de ejecución y ejecútalo:

   ```bash
   # Añade permiso de ejecución al script de Bash.
   chmod +x consultas-mariadb.sh
   # Ejecuta el script desde el directorio actual.
   ./consultas-mariadb.sh
   ```

   Para ejecutar una única consulta desde Bash también puedes utilizar una cadena redirigida a la entrada estándar:

   ```bash
   # Envía una consulta SQL por la entrada estándar y muestra las bases de datos.
   sudo mariadb <<< "SHOW DATABASES;"
   ```

   No guardes contraseñas en el script ni las escribas directamente en la línea de comandos. Para automatizaciones reales utiliza un fichero de opciones protegido o un mecanismo de secretos adecuado.

### Resultado esperado de la práctica 4

`listar-bases.sql` muestra las bases de datos de MariaDB y `listar-usuarios.sql` muestra cada usuario junto con su `Host`. `consultas-mariadb.sh` ejecuta ambas consultas sin entrar manualmente en la consola.

#### Captura de pantalla de la práctica 4

Envía una sola captura de la terminal donde se vean las ejecuciones de `listar-bases.sql`, `listar-usuarios.sql` y `consultas-mariadb.sh`, junto con sus resultados.

### Recuperación de la práctica 4

Los scripts son archivos de trabajo locales. Cuando ya no los necesites, puedes eliminarlos así:

```bash
# Elimina únicamente los tres scripts creados para esta práctica.
rm listar-bases.sql listar-usuarios.sql consultas-mariadb.sh
```

No elimines ningún fichero de configuración ni ningún dato de MariaDB como parte de esta limpieza.

---

## Entrega final

Crea un fichero Markdown con una sección para cada práctica. En cada sección incluye:

- comandos ejecutados;
- ficheros de configuración o scripts creados y modificados;
- capturas o salidas que demuestren el resultado;
- errores encontrados y su solución;
- forma de restaurar el estado inicial.
