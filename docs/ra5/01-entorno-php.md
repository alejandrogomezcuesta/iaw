# Entorno de iniciación a PHP

Para empezar a escribir y ejecutar programas PHP en Linux necesitas PHP CLI
(la interfaz de línea de comandos) y un editor de texto, como Visual Studio
Code. No hace falta instalar un servidor web para ejecutar los ejemplos de
esta unidad.

## Instalar PHP CLI en Linux

En Debian y Ubuntu, abre una terminal y ejecuta:

```bash
sudo apt update
sudo apt install php-cli
```

En otras distribuciones, instala el paquete equivalente a `php-cli` con el
gestor de paquetes correspondiente.

Comprueba que PHP se ha instalado correctamente:

```bash
php -v
```

El comando debe mostrar la versión de PHP instalada.

## Visual Studio Code

Puedes escribir el código PHP en Visual Studio Code y ejecutarlo desde su
terminal integrada. No necesitas instalar una extensión para ejecutar PHP:
la ejecución la realiza el comando `php` que instalaste en el sistema.

Como ayuda para editar, puedes instalar **PHP Intelephense**, que ofrece
completado de código y avisos sobre errores. 

!!!important "Abrir una carpeta en Visual Studio Code"
    Para trabajar con PHP en esta unidad, abre siempre en Visual Studio Code la
    carpeta del proyecto, no solo el fichero `.php`. Selecciona **Archivo >
    Abrir carpeta...** y elige la carpeta donde guardarás tus programas. Así
    podrás ver los ficheros del proyecto y ejecutar tus programas desde la
    terminal integrada, situada en esa carpeta.

## Crear y ejecutar el primer programa

Guarda este código en un fichero llamado `hola.php`:

```php linenums="1"
<?php
print("Hola, mundo" . PHP_EOL);
```

Abre la terminal integrada de Visual Studio Code, sitúate en la carpeta donde
guardaste el fichero y ejecútalo:

```bash
php hola.php
```

La salida será:

```text
Hola, mundo
```

Si el comando `php` no se reconoce, comprueba que PHP CLI está instalado y
reinicia la terminal para que actualice el entorno.