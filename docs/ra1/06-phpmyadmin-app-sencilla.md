# Práctica: phpMyAdmin y aplicación web sencilla

## Objetivo

Desplegar en una instancia EC2 de AWS una pila LAMP con **MariaDB** y utilizarla para servir phpMyAdmin y una aplicación web sencilla de altas, consultas, modificaciones y borrados.

> En esta práctica, cualquier instrucción de los recursos enlazados que mencione MySQL se aplica a MariaDB. No instales MySQL.

## 1. Preparar la instancia y la pila LAMP

1. Crea una instancia EC2 nueva para esta práctica y llámala **"phpMyAdmin y app sencilla"**. Utiliza la versión más reciente de Ubuntu Server y permite el acceso SSH y HTTP desde el grupo de seguridad. HTTPS no va a hacer falta pero acostúmbrate a ponerlo.
2. Conéctate por SSH y ejecuta el script de instalación de la pila LAMP que preparaste en una actividad anterior. El script debe funcionar en esta instancia e instalar Apache, MariaDB y PHP, incluido el soporte de PHP para conectarse a MariaDB.
3. Comprueba que Apache, PHP y MariaDB funcionan correctamente siguiendo las indicaciones de [Instalación de un servidor web LAMP en Debian](02-instalar-lamp-debian.md). No continúes hasta resolver cualquier error: tanto phpMyAdmin como la aplicación dependen de esta pila.


## 2. Instalar y comprobar phpMyAdmin

Instala phpMyAdmin directamente desde los repositorios de Ubuntu. No es necesario consultar ninguna guía externa.

1. Instala phpMyAdmin y los módulos PHP que necesita:

   ```bash
   sudo apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl -y
   ```

2. Cuando el instalador pregunte qué servidor web debe configurar, selecciona `apache2` con la barra espaciadora y confirma.
3. Confirma que quieres utilizar `dbconfig-common` para configurar la base de datos de phpMyAdmin.
4. Introduce y confirma la contraseña que solicite el instalador para phpMyAdmin. Es una contraseña de configuración del paquete; para iniciar sesión en la interfaz usarás una cuenta de MariaDB.

En las preguntas del instalador, utiliza MariaDB como gestor de bases de datos. Al terminar, abre `http://IP_PUBLICA/phpmyadmin`, sustituyendo `IP_PUBLICA` por la IPv4 pública de la instancia, e inicia sesión con un usuario de MariaDB. Comprueba que aparece la interfaz y que puedes consultar las bases de datos.

## 3. Desplegar la aplicación web

El código de referencia de esta actividad está en el [repositorio original iaw-practica-lamp](https://github.com/josejuansanchez/iaw-practica-lamp). El enlace es solo de consulta: no hace falta abrirlo, clonar el repositorio ni descargar ficheros. Los comandos siguientes crean una aplicación CRUD equivalente directamente en la instancia.

### 3.1 Crear la base de datos, el usuario y la tabla

Abre la consola de MariaDB:

```bash
sudo mariadb
```

Ejecuta estas instrucciones. Cambia `CAMBIA_ESTA_CONTRASENA` por una contraseña propia y apúntala para usarla en el fichero de configuración del siguiente paso:

```sql
CREATE DATABASE lamp_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'CAMBIA_ESTA_CONTRASENA';
GRANT SELECT, INSERT, UPDATE, DELETE ON lamp_db.* TO 'app_user'@'localhost';
EXIT;
```

Crea la tabla y añade dos registros de ejemplo para que la aplicación muestre datos desde la primera visita:

```bash
sudo mariadb lamp_db <<'SQL'
CREATE TABLE users (
   id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
   name VARCHAR(100) NOT NULL,
   age SMALLINT UNSIGNED NOT NULL,
   email VARCHAR(100) NOT NULL UNIQUE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;

INSERT INTO users (name, age, email) VALUES
   ('Ana García', 25, 'ana@example.test'),
   ('Luis Pérez', 31, 'luis@example.test');
SQL
```

En phpMyAdmin, comprueba que aparece la base de datos `lamp_db`, la tabla `users` y los dos registros. La aplicación utilizará `app_user`, no la cuenta `root`.

### 3.2 Crear la configuración de conexión

Guarda las credenciales fuera del directorio público de Apache:

```bash
sudo tee /var/www/app-config.php >/dev/null <<'PHP'
<?php
declare(strict_types=1);

$database = new PDO(
   'mysql:host=localhost;dbname=lamp_db;charset=utf8mb4',
   'app_user',
   'CAMBIA_ESTA_CONTRASENA',
   [
      PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION,
      PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
      PDO::ATTR_EMULATE_PREPARES => false,
   ]
);
PHP

sudo chown root:www-data /var/www/app-config.php
sudo chmod 640 /var/www/app-config.php
```

Edita el comando y sustituye también en este fichero `CAMBIA_ESTA_CONTRASENA` por la misma contraseña que asignaste al usuario de MariaDB.

### 3.3 Crear la aplicación

Crea un directorio propio para no sobrescribir la página de bienvenida que Apache instala por defecto:

```bash
sudo install -d -o root -g www-data -m 755 /var/www/html/app
```

Crea `/var/www/html/app/index.php` con este código. Incluye listado, alta, edición y borrado de usuarios; las operaciones usan consultas preparadas y token CSRF:

```bash
sudo tee /var/www/html/app/index.php >/dev/null <<'PHP'
<?php
declare(strict_types=1);

session_start();
require '/var/www/app-config.php';

if (!isset($_SESSION['csrf_token'])) {
   $_SESSION['csrf_token'] = bin2hex(random_bytes(32));
}

function escape(string|int|null $value): string
{
   return htmlspecialchars((string) $value, ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8');
}

function redirect(string $state): never
{
   header('Location: /app/?estado=' . rawurlencode($state));
   exit;
}

$error = '';

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
   $token = $_POST['csrf_token'] ?? '';
   if (!is_string($token) || !hash_equals($_SESSION['csrf_token'], $token)) {
      http_response_code(400);
      exit('La solicitud no es válida. Recarga la página e inténtalo de nuevo.');
   }

   $action = $_POST['action'] ?? '';

   if ($action === 'delete') {
      $id = filter_var($_POST['id'] ?? null, FILTER_VALIDATE_INT, ['options' => ['min_range' => 1]]);
      if ($id === false) {
         $error = 'El identificador no es válido.';
      } else {
         $statement = $database->prepare('DELETE FROM users WHERE id = ?');
         $statement->execute([$id]);
         redirect('borrado');
      }
   } elseif ($action === 'create' || $action === 'update') {
      $name = trim((string) ($_POST['name'] ?? ''));
      $age = filter_var($_POST['age'] ?? null, FILTER_VALIDATE_INT, ['options' => ['min_range' => 0, 'max_range' => 150]]);
      $email = trim((string) ($_POST['email'] ?? ''));
      $id = filter_var($_POST['id'] ?? null, FILTER_VALIDATE_INT, ['options' => ['min_range' => 1]]);

      if ($name === '' || strlen($name) > 100 || $age === false || !filter_var($email, FILTER_VALIDATE_EMAIL) || strlen($email) > 100) {
         $error = 'Revisa el nombre, la edad (0-150) y el correo electrónico.';
      } elseif ($action === 'update' && $id === false) {
         $error = 'El identificador no es válido.';
      } else {
         try {
            if ($action === 'create') {
               $statement = $database->prepare('INSERT INTO users (name, age, email) VALUES (?, ?, ?)');
               $statement->execute([$name, $age, $email]);
               redirect('creado');
            }

            $statement = $database->prepare('UPDATE users SET name = ?, age = ?, email = ? WHERE id = ?');
            $statement->execute([$name, $age, $email, $id]);
            redirect('actualizado');
         } catch (PDOException $exception) {
            if ($exception->getCode() === '23000') {
               $error = 'Ya existe un usuario con ese correo electrónico.';
            } else {
               throw $exception;
            }
         }
      }
   }
}

$editing = null;
if (isset($_GET['editar'])) {
   $editId = filter_var($_GET['editar'], FILTER_VALIDATE_INT, ['options' => ['min_range' => 1]]);
   if ($editId !== false) {
      $statement = $database->prepare('SELECT id, name, age, email FROM users WHERE id = ?');
      $statement->execute([$editId]);
      $editing = $statement->fetch() ?: null;
   }
}

$users = $database->query('SELECT id, name, age, email FROM users ORDER BY id')->fetchAll();
$messages = [
   'creado' => 'Registro creado.',
   'actualizado' => 'Registro actualizado.',
   'borrado' => 'Registro eliminado.',
];
$notice = $messages[$_GET['estado'] ?? ''] ?? '';
?>
<!doctype html>
<html lang="es">
<head>
   <meta charset="utf-8">
   <meta name="viewport" content="width=device-width, initial-scale=1">
   <title>Gestión de usuarios</title>
   <style>
      body { max-width: 900px; margin: 2rem auto; padding: 0 1rem; font: 1rem/1.5 sans-serif; color: #20242a; }
      h1, h2 { line-height: 1.2; }
      form { margin: 0 0 1rem; }
      label { display: inline-block; margin: .4rem .7rem .4rem 0; }
      input { display: block; box-sizing: border-box; width: 100%; padding: .45rem; }
      button { padding: .45rem .7rem; cursor: pointer; }
      table { width: 100%; border-collapse: collapse; margin-top: 1rem; }
      th, td { padding: .55rem; border-bottom: 1px solid #c8cdd2; text-align: left; }
      .actions { display: flex; gap: .5rem; align-items: center; }
      .actions form { margin: 0; }
      .notice { color: #176b3a; }
      .error { color: #a32222; }
      @media (max-width: 600px) { table { font-size: .9rem; } th, td { padding: .35rem .2rem; } }
   </style>
</head>
<body>
   <h1>Gestión de usuarios</h1>

   <?php if ($notice !== ''): ?><p class="notice"><?= escape($notice) ?></p><?php endif; ?>
   <?php if ($error !== ''): ?><p class="error"><?= escape($error) ?></p><?php endif; ?>

   <h2><?= $editing ? 'Editar usuario' : 'Añadir usuario' ?></h2>
   <form method="post">
      <input type="hidden" name="csrf_token" value="<?= escape($_SESSION['csrf_token']) ?>">
      <input type="hidden" name="action" value="<?= $editing ? 'update' : 'create' ?>">
      <?php if ($editing): ?><input type="hidden" name="id" value="<?= escape($editing['id']) ?>"><?php endif; ?>
      <label>Nombre
         <input name="name" maxlength="100" required value="<?= escape($editing['name'] ?? '') ?>">
      </label>
      <label>Edad
         <input name="age" type="number" min="0" max="150" required value="<?= escape($editing['age'] ?? '') ?>">
      </label>
      <label>Correo electrónico
         <input name="email" type="email" maxlength="100" required value="<?= escape($editing['email'] ?? '') ?>">
      </label>
      <button type="submit"><?= $editing ? 'Guardar cambios' : 'Añadir usuario' ?></button>
      <?php if ($editing): ?> <a href="/app/">Cancelar</a><?php endif; ?>
   </form>

   <h2>Usuarios registrados</h2>
   <table>
      <thead><tr><th>Nombre</th><th>Edad</th><th>Correo</th><th>Acciones</th></tr></thead>
      <tbody>
      <?php foreach ($users as $user): ?>
         <tr>
            <td><?= escape($user['name']) ?></td>
            <td><?= escape($user['age']) ?></td>
            <td><?= escape($user['email']) ?></td>
            <td class="actions">
               <a href="/app/?editar=<?= escape($user['id']) ?>">Editar</a>
               <form method="post" onsubmit="return confirm('¿Quieres borrar este registro?')">
                  <input type="hidden" name="csrf_token" value="<?= escape($_SESSION['csrf_token']) ?>">
                  <input type="hidden" name="action" value="delete">
                  <input type="hidden" name="id" value="<?= escape($user['id']) ?>">
                  <button type="submit">Borrar</button>
               </form>
            </td>
         </tr>
      <?php endforeach; ?>
      <?php if (!$users): ?><tr><td colspan="4">Todavía no hay usuarios.</td></tr><?php endif; ?>
      </tbody>
   </table>
</body>
</html>
PHP

sudo chown root:www-data /var/www/html/app/index.php
sudo chmod 644 /var/www/html/app/index.php
```

### 3.4 Comprobar el despliegue

Comprueba la sintaxis de los dos ficheros PHP y que el usuario de la aplicación puede leer la tabla:

```bash
sudo php -l /var/www/app-config.php
sudo php -l /var/www/html/app/index.php
mariadb -u app_user -p lamp_db -e "SELECT id, name, age, email FROM users;"
```

Abre `http://IP_PUBLICA/app/`, sustituye `IP_PUBLICA` por la IPv4 pública de la instancia y prueba a añadir, editar y borrar un registro. phpMyAdmin estará disponible en `http://IP_PUBLICA/phpmyadmin`. Si hay un error de conexión, comprueba la contraseña en `/var/www/app-config.php`, que el paquete `php-mysql` esté instalado y que Apache pueda leer ese fichero.

La imagen siguiente es una referencia visual de la aplicación funcionando; no sustituye las capturas que se piden como entregables.

![Ejemplo de la aplicación funcionando](image1.png)

## Entregables

- Una captura de pantalla donde se vea phpMyAdmin funcionando.
- Una captura de pantalla donde se vea la aplicación funcionando.