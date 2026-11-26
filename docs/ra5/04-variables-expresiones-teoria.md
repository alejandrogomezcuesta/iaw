# Variables y expresiones en PHP

En un programa podemos necesitar recordar datos para utilizarlos más adelante.
Por ejemplo, un juego puede guardar el nombre del jugador, sus puntos de vida,
su nivel o el oro que ha conseguido. Para almacenar y trabajar con esos datos
utilizamos variables y expresiones.

## 1. Crear y ejecutar un programa PHP

Guarda el código de cada ejemplo en un fichero con extensión `.php`. Un
programa PHP comienza con la etiqueta `<?php`; las instrucciones terminan con
punto y coma (`;`).

```php linenums="1"
<?php
print("Hola, mundo" . PHP_EOL);
```

Puedes ejecutarlo desde la terminal integrada de Visual Studio Code, abierta
en la carpeta del proyecto:

```bash
php hola.php
```

`print` muestra un valor en la salida. `PHP_EOL` añade un salto de línea.
También se puede usar `echo`, pero en estos apuntes usaremos `print`.

## 2. Qué es una variable

Una variable es un nombre asociado a un valor que el programa puede utilizar
más adelante. Por ejemplo, `$vida` puede guardar los puntos de vida de un
personaje:

```php linenums="1"
<?php
$vida = 100;
print($vida . PHP_EOL);
```

En PHP, el nombre de una variable siempre empieza por `$`. Después del signo
`$`, el nombre debe comenzar por una letra o un guion bajo (`_`); puede
contener letras, números y guiones bajos, pero no espacios ni guiones. Por
ejemplo:

```php
$vida = 100;
$vida_jugador = 100;
$_nivel = 1;
```

`$1vida`, `$vida-del-jugador` y `$vida jugador` no son nombres válidos. Los
nombres distinguen entre mayúsculas y minúsculas: `$vida` y `$Vida` son
variables diferentes. Conviene utilizar nombres descriptivos y no usar
palabras reservadas del lenguaje.

El signo `=` es el operador de asignación: guarda el valor de la derecha en la
variable de la izquierda. No significa una igualdad matemática.

## 3. Asignar valores y tipos de datos

PHP determina automáticamente el tipo de una variable a partir del valor que
se le asigna. En estos ejemplos aparecen texto, números enteros y números
decimales:

```php linenums="1"
<?php
$nombre_jugador = "Alex";
$vida = 100;
$oro = 5000;
$velocidad = 15.5;
$nivel = 1;

print($nombre_jugador . PHP_EOL);
print($vida . PHP_EOL);
print($oro . PHP_EOL);
print($velocidad . PHP_EOL);
print($nivel . PHP_EOL);
```

Los tipos que utilizaremos principalmente son:

- **String (`string`)**: texto entre comillas simples o dobles, como
  `"Luna"` o `'Hola'`.
- **Integer (`int`)**: número entero, como `100`, `0` o `-5`.
- **Float (`float`)**: número con decimales, como `15.5` o `99.9`. En el
  código se escribe un punto como separador decimal.

Puedes consultar el tipo de una variable con `gettype()`:

```php linenums="1"
<?php
$nombre = "Carlos";
$vida = 100;
$velocidad = 12.5;

print(gettype($nombre) . PHP_EOL);    // string
print(gettype($vida) . PHP_EOL);      // integer
print(gettype($velocidad) . PHP_EOL); // double (tipo float)
```

## 4. Cambiar el valor de una variable

Una variable puede recibir un nuevo valor. Al asignárselo, el valor anterior
deja de ser el valor actual de esa variable:

```php linenums="1"
<?php
$vida = 100;
print($vida . PHP_EOL);

$vida = 75;
print($vida . PHP_EOL);

$vida = 100;
print($vida . PHP_EOL);
```

También se puede actualizar una variable usando su valor actual. Estas dos
formas son equivalentes:

```php linenums="1"
<?php
$oro = 10;
$oro = $oro + 5;
print($oro . PHP_EOL); // 15

$oro += 3;
print($oro . PHP_EOL); // 18
```

`+=` suma el valor de la derecha a la variable de la izquierda. De forma
similar, `-=`, `*=`, y `/=` permiten restar, multiplicar o dividir y guardar
el resultado en la misma variable.

## 5. Mostrar variables junto con texto

Para mostrar varias piezas de información en una línea puedes concatenar
texto y valores con el operador punto (`.`):

```php linenums="1"
<?php
$nombre = "Lucas";
$vida = 100;

print("El jugador " . $nombre . " tiene " . $vida . " puntos de vida."
    . PHP_EOL);
```

También puedes interpolar variables dentro de una cadena entre comillas
dobles. Las llaves delimitan claramente el nombre de cada variable:

```php linenums="1"
<?php
$nombre = "Lucas";
$vida = 100;

print("El jugador {$nombre} tiene {$vida} puntos de vida." . PHP_EOL);
```

En una cadena entre comillas simples, PHP no sustituye las variables por sus
valores. Para estos apuntes, utiliza comillas dobles cuando quieras
interpolarlas.

En PHP, `+` es para sumar números y `.` es para concatenar cadenas de texto.
No uses `+` para unir texto.

## 6. Expresiones y operaciones aritméticas

Una expresión combina valores, variables y operadores para producir un
resultado. Los operadores aritméticos básicos son:

| Operador | Operación |
| --- | --- |
| `+` | Suma |
| `-` | Resta |
| `*` | Multiplicación |
| `/` | División |
| `**` | Potencia |
| `%` | Resto de la división |

Se pueden usar directamente con números:

```php linenums="1"
<?php
print((10 + 5) . PHP_EOL);  // 15
print((20 - 8) . PHP_EOL);  // 12
print((4 * 6) . PHP_EOL);   // 24
print((100 / 4) . PHP_EOL); // 25
print((2 ** 3) . PHP_EOL);  // 8
```

También se pueden guardar los operandos en variables y almacenar el resultado
de la expresión en otra variable:

```php linenums="1"
<?php
$dano_base = 25;
$bonificacion = 15;
$dano_total = $dano_base + $bonificacion;

print("Daño total: {$dano_total}" . PHP_EOL);
```

PHP calcula primero las multiplicaciones y divisiones y después las sumas y
restas. Utiliza paréntesis para indicar claramente el orden que necesitas.
Por ejemplo, el promedio de tres calificaciones se calcula sumándolas y
dividiendo el total entre tres:

```php
$promedio = ($nota1 + $nota2 + $nota3) / 3;
```

El operador `/` puede producir un número entero si la división es exacta
entre enteros; si no, el resultado es decimal. Por ejemplo, `10 / 2` da `5`,
mientras que `10 / 4` da `2.5`.

## 7. Fórmulas y cadenas de cálculos

Las expresiones también permiten aplicar fórmulas. Para convertir una
temperatura de grados Celsius a Fahrenheit se puede calcular:

```php
$fahrenheit = ($celsius * 9 / 5) + 32;
```

Para calcular el IVA del 21 % de un precio y su precio final:

```php
$iva = round($precio * 0.21, 2);
$precio_final = round($precio + $iva, 2);
```

Las mismas operaciones sirven para aplicar otras fórmulas de los ejercicios:

| Cálculo | Fórmula |
| --- | --- |
| Área de un rectángulo | `$area = $ancho * $alto;` |
| Promedio de tres valores | `$promedio = ($nota1 + $nota2 + $nota3) / 3;` |
| Cambio de una compra | `$cambio = $dinero_entregado - $precio;` |
| Velocidad media | `$velocidad = $distancia / $tiempo;` |
| Perímetro de un círculo | `$perimetro = 2 * $pi * $radio;` |

`round($valor, 2)` redondea el resultado a dos decimales. En cálculos con
decimales pueden aparecer pequeñas diferencias de representación; el
redondeo es útil para mostrar cantidades monetarias.

Una variable calculada puede utilizarse en otra expresión. Así se forma una
cadena de cálculos:

```php linenums="1"
<?php
$vida_base = 100;
$bonificacion = 25;
$vida_total = $vida_base + $bonificacion;

$dano_recibido = 30;
$vida_actual = $vida_total - $dano_recibido;

$curacion = 20;
$vida_actual = $vida_actual + $curacion;

print("Vida total: {$vida_total}" . PHP_EOL);
print("Vida actual: {$vida_actual}" . PHP_EOL);
```

## 8. Ejemplo completo

Este ejemplo combina variables, tipos, expresiones, reasignación y salida:

```php linenums="1"
<?php
// Datos del jugador
$nombre = "Warrior";
$vida_base = 100;
$armadura = 20;

// Calcular la vida y actualizarla durante el juego
$vida_total = $vida_base + $armadura;
$dano_recibido = 35;
$vida_actual = $vida_total - $dano_recibido;
$curacion = 15;
$vida_actual = $vida_actual + $curacion;

// Calcular el oro
$oro_inicial = 1000;
$oro_ganado = 500;
$oro_gastado = 200;
$oro_final = $oro_inicial + $oro_ganado - $oro_gastado;

// Mostrar el estado del jugador
print("=== ESTADO DEL JUGADOR ===" . PHP_EOL);
print("Nombre: {$nombre}" . PHP_EOL);
print("Vida: {$vida_actual}/{$vida_total}" . PHP_EOL);
print("Oro: {$oro_final}" . PHP_EOL);
```

## Resumen

- Una variable PHP empieza por `$` y recibe un valor mediante `=`.
- PHP reconoce, entre otros, valores de tipo `string`, `int` y `float`.
- Puedes reasignar una variable o actualizarla con operaciones como `+=`.
- `print` muestra información; `PHP_EOL` añade un salto de línea.
- Usa `.` para concatenar texto y variables; `+` suma números.
- Las expresiones combinan números, variables y operadores.
- Los paréntesis ayudan a expresar el orden de las operaciones.
- Una fórmula puede guardarse en una variable y usarse después en otros
  cálculos.

Con estos conceptos puedes resolver los ejercicios de variables, operaciones,
conversiones, porcentajes, áreas, promedios, cambios, velocidades y perímetros.
