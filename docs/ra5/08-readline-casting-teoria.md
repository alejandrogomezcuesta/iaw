# Entrada de datos y conversión de tipos en PHP

Hasta ahora, los datos de los programas estaban escritos en el propio código.
Con la entrada de datos podemos pedir información al usuario y utilizarla en
cálculos. En PHP, los ejemplos interactivos de esta unidad se ejecutan desde
la terminal.

## 1. Leer datos con `readline()`

La función `readline()` muestra una pregunta, espera a que el usuario escriba
una respuesta y pulse Enter, y devuelve lo que ha escrito:

```php linenums="1"
<?php
$nombre = readline("¿Cuál es tu nombre? ");

print("¡Hola, {$nombre}!" . PHP_EOL);
print("Qué gusto conocerte, {$nombre}." . PHP_EOL);
```

Guarda el código en un fichero, por ejemplo `saludo.php`, y ejecútalo desde
la terminal integrada de Visual Studio Code:

```bash
php saludo.php
```

La terminal espera a que escribas la respuesta. Escribe el texto y pulsa
Enter para que el programa continúe. La función `readline()` está pensada
para programas de consola; ejecútalos en una terminal que permita introducir
texto, no en un panel de salida que no admita entrada interactiva.

## 2. La entrada se recibe como texto

Aunque el usuario escriba números, `readline()` devuelve una cadena de texto
(`string`). Puedes comprobarlo con `gettype()`:

```php linenums="1"
<?php
$entrada = readline("Escribe un número: ");

print("El valor es: {$entrada}" . PHP_EOL);
print("El tipo de dato es: " . gettype($entrada) . PHP_EOL);
```

Si escribes `42`, el contenido de `$entrada` sigue siendo el texto `"42"`.
Para hacer cálculos, convierte la entrada al tipo numérico que corresponda.

En PHP, el operador punto (`.`) concatena cadenas. Por ejemplo, concatena
`"5"` y `"3"` para formar `"53"`. No es lo mismo que sumar dos números:

```php linenums="1"
<?php
$texto1 = "5";
$texto2 = "3";
$concatenacion = $texto1 . $texto2;
$suma = (int) $texto1 + (int) $texto2;

print("Texto unido: {$concatenacion}" . PHP_EOL); // 53
print("Suma: {$suma}" . PHP_EOL);                 // 8
```

Aunque PHP puede convertir automáticamente algunas cadenas numéricas en
operaciones, es mejor convertir explícitamente las entradas antes de hacer
cálculos.

## 3. Convertir texto a números

En PHP se puede convertir un valor usando un *cast* (conversión de tipo). El
cast se escribe entre paréntesis antes del valor:

| Cast | Tipo resultante | Uso habitual |
| --- | --- | --- |
| `(int)` | Entero (`int`) | Edades, cantidades enteras |
| `(float)` | Decimal (`float`) | Precios, medidas y otros valores con decimales |

Puedes convertir una variable después de leerla:

```php linenums="1"
<?php
$edad_texto = readline("¿Cuántos años tienes? ");
$edad = (int) $edad_texto;

print("En 5 años tendrás " . ($edad + 5) . " años." . PHP_EOL);
```

O hacer la lectura y la conversión en una sola instrucción:

```php linenums="1"
<?php
$edad = (int) readline("¿Cuántos años tienes? ");
print("En 5 años tendrás " . ($edad + 5) . " años." . PHP_EOL);
```

Utiliza `(int)` cuando esperes un número entero y `(float)` cuando la entrada
pueda tener decimales:

```php linenums="1"
<?php
$peso = (float) readline("Introduce tu peso en kg: ");
$altura = (float) readline("Introduce tu altura en metros: ");
$imc = $peso / ($altura ** 2);

print("Tu IMC es: {$imc}" . PHP_EOL);
```

Para introducir decimales, escribe un punto, por ejemplo `1.75`.

Las funciones `intval()` y `floatval()` también convierten a entero y decimal,
respectivamente. En estos apuntes usaremos principalmente los casts `(int)` y
`(float)`.

## 4. Validar antes de convertir

No conviertas una respuesta a número sin comprobar que contiene un valor
numérico. Por ejemplo, en PHP `(int) "hola"` da `0`, no el mismo tipo de error
que produciría la conversión equivalente en Python. La función
`is_numeric()` permite comprobar la entrada antes de convertirla:

```php linenums="1"
<?php
$entrada = readline("Introduce un número: ");

if (is_numeric($entrada)) {
    $numero = (float) $entrada;
    print("El doble es: " . ($numero * 2) . PHP_EOL);
} else {
    print("La entrada no es un número válido." . PHP_EOL);
}
```

Un cast `(int)` elimina la parte decimal; por ejemplo, `(int) "3.14"` produce
`3`. Si necesitas conservar los decimales, usa `(float)`.

## 5. Mostrar resultados y dar formato

Puedes mostrar texto y variables concatenándolos con `.` o interpolando las
variables en una cadena con comillas dobles:

```php
print("El área es: " . $area . PHP_EOL);
print("El área es: {$area}" . PHP_EOL);
```

Para mostrar una cantidad con dos decimales, puedes utilizar
`number_format()`. Sus parámetros permiten indicar el número de decimales, el
separador decimal y el separador de millares:

```php linenums="1"
<?php
$precio = 1234.5;
print(number_format($precio, 2, ',', '.') . " €" . PHP_EOL); // 1.234,50 €
```

## 6. Ejemplo: calcular el área de un rectángulo

La fórmula del área es ancho multiplicado por alto. Como las medidas pueden
tener decimales, las leemos como valores `float`:

```php linenums="1"
<?php
$ancho = (float) readline("Introduce el ancho del rectángulo: ");
$alto = (float) readline("Introduce el alto del rectángulo: ");
$area = $ancho * $alto;

print("El área del rectángulo es: {$area}" . PHP_EOL);
```

También se pueden leer los datos necesarios para calcular el área de un
círculo. Usa `3.14159` como aproximación de π:

```php linenums="1"
<?php
$radio = (float) readline("Introduce el radio del círculo: ");
$pi = 3.14159;
$area = $pi * ($radio ** 2);

print("El área del círculo es: {$area}" . PHP_EOL);
```

## 7. Ejemplo: calculadora simple

Al convertir las entradas a números, los operadores realizan operaciones
matemáticas. Comprueba que el divisor no sea cero antes de dividir:

```php linenums="1"
<?php
$numero1 = (float) readline("Primer número: ");
$numero2 = (float) readline("Segundo número: ");

$suma = $numero1 + $numero2;
$resta = $numero1 - $numero2;
$multiplicacion = $numero1 * $numero2;

print("{$numero1} + {$numero2} = {$suma}" . PHP_EOL);
print("{$numero1} - {$numero2} = {$resta}" . PHP_EOL);
print("{$numero1} * {$numero2} = {$multiplicacion}" . PHP_EOL);

if ($numero2 != 0) {
    $division = $numero1 / $numero2;
    print("{$numero1} / {$numero2} = {$division}" . PHP_EOL);
} else {
    print("No se puede dividir entre cero." . PHP_EOL);
}
```

## 8. Conceptos para los ejercicios

Los ejercicios combinan la entrada y conversión de datos con expresiones y
condiciones. Estas fórmulas se aplican con los operadores aritméticos de PHP. Algunos recordatorios:

`**` eleva un número a una potencia, como en `$altura ** 2`; `%` obtiene el
resto de una división entera, que es `0` cuando un número es par.

Para decisiones se utilizan `if`, `elseif` y `else`. Las condiciones llevan
paréntesis y los bloques llaves; cada instrucción termina con `;`:

```php linenums="1"
<?php
if ($valor < 10) {
    print("Menor que 10" . PHP_EOL);
} elseif ($valor <= 20) {
    print("Entre 10 y 20" . PHP_EOL);
} else {
    print("Mayor que 20" . PHP_EOL);
}
```

Las comparaciones de cadenas se pueden hacer con `===`, y `number_format()`
permite mostrar importes con dos decimales. Para leer una respuesta de sí o
no, compara el texto recibido con el valor esperado, por ejemplo
`$respuesta === "si"`.

Para una factura con cupón, primero calcula el total con IVA y después aplica
el descuento solo si la respuesta indica que hay cupón. Por ejemplo, un
descuento del 10 % se calcula multiplicando el importe con IVA por `0.10` y
restando ese descuento.

## Comprueba lo aprendido

- ¿Qué ocurre al ejecutar `readline()`? El programa espera a que el usuario
  escriba una respuesta y pulse Enter.
- Si se introduce `123`, ¿qué tipo de dato devuelve `readline()`? Devuelve
  el texto `"123"`, de tipo `string`.
- ¿Cuál es la diferencia entre `(int)` y `(float)`? El primero convierte a
  entero y el segundo permite conservar decimales.
- ¿Qué ocurre al convertir `"hola"` con `(int)`? El resultado es `0`; usa
  `is_numeric()` para comprobar una entrada antes de convertirla.

## Resumen

- `readline("Pregunta: ")` pide una respuesta en la terminal y la devuelve
  como `string`.
- Convierte entradas enteras con `(int)` y entradas decimales con `(float)`.
- Comprueba con `is_numeric()` que una entrada sea numérica antes de
  convertirla si el programa necesita validarla.
- Usa `.` para concatenar texto, `+` para sumar números y `number_format()`
  para presentar cantidades decimales.
- Aplica `if`, `elseif` y `else` para tomar decisiones según los datos
  introducidos.
