# Literales, tipos y expresiones en PHP

PHP es un lenguaje de programación que se utiliza, entre otras cosas, para
crear páginas web dinámicas. En esta unidad aprenderás a escribir valores
directamente en el código, reconocer sus tipos y formar expresiones para que
PHP las evalúe.

## 1. Escribir y ejecutar un programa PHP

Guarda el código en un fichero con extensión `.php`, por ejemplo,
`programa.php`. El código PHP comienza con la etiqueta `<?php`. Las
instrucciones se suelen terminar con punto y coma (`;`).

```php linenums="1"
<?php
print("Hola");
```

Si tienes PHP instalado, puedes ejecutar el fichero desde una terminal:

```bash
php programa.php
```

PHP ejecuta las instrucciones en orden, de arriba hacia abajo. Una expresión
es evaluada, pero por sí sola no muestra el resultado en la terminal: para
mostrarlo hay que usar una instrucción de salida como `print`.

```php linenums="1"
<?php
1 + 2;            // Se calcula, pero no se muestra.
print(3 + 5);     // Muestra 8.
```

`print` no añade un salto de línea automáticamente. Para terminar una línea
en la terminal puedes concatenar la constante `PHP_EOL`:

```php linenums="1"
<?php
print("Primera línea" . PHP_EOL);
print("Segunda línea" . PHP_EOL);
```

## 2. Literales: datos escritos directamente

Un **literal** es un valor escrito directamente en el programa. Algunos
ejemplos son:

```php linenums="1"
<?php
5;
"Hola, mundo";
3.14;
true;
```

Los literales pueden formar parte de expresiones y se pueden mostrar con
`print`:

```php linenums="1"
<?php
print(42);
print("Hola" . PHP_EOL);
print((1 + 2) . PHP_EOL);
```

El número `42` y el texto `"42"` pueden parecer iguales al mostrarse, pero
son valores de tipos distintos: el primero es numérico y el segundo es texto.

## 3. Tipos de datos básicos

El **tipo de dato** indica qué clase de valor se está utilizando. Los tipos
básicos de esta unidad son:

### 3.1 `int`: números enteros

Un `int` es un número entero, sin parte decimal. Puede ser positivo, negativo
o cero.

```php linenums="1"
<?php
5;
-10;
0;
1000;
```

### 3.2 `float`: números decimales

Un `float` representa un número con parte decimal. En PHP se escribe un punto
(`.`) como separador decimal.

```php linenums="1"
<?php
3.14;
-2.5;
0.0;
99.99;
```

### 3.3 `string`: cadenas de texto

Un `string` es texto: una palabra, una frase o una secuencia de caracteres.
Se escribe entre comillas simples o dobles.

```php linenums="1"
<?php
'Hola';
"PHP es un lenguaje de programación";
"42";
'¡Hola, mundo!';
```

Las comillas forman parte de la sintaxis, no del texto que se muestra. Por
eso, `42` es un entero y `"42"` es una cadena, aunque `print` muestre `42` en
ambos casos.

### 3.4 `bool`: valores lógicos

Un `bool` (booleano) solo puede ser `true` (verdadero) o `false` (falso). Se
escriben sin comillas.

```php linenums="1"
<?php
true;
false;
```

Con comillas, `"true"` y `"false"` son cadenas de texto, no valores
booleanos.

## 4. Consultar el tipo de un valor

La función `gettype()` devuelve una cadena con el tipo de un valor. En PHP,
sus nombres habituales son `integer`, `double`, `string` y `boolean`:

```php linenums="1"
<?php
print(gettype(5) . PHP_EOL);       // integer
print(gettype(3.14) . PHP_EOL);    // double
print(gettype("Hola") . PHP_EOL);  // string
print(gettype(true) . PHP_EOL);    // boolean
```

También puedes usar `var_dump()` para inspeccionar el tipo y el valor durante
el aprendizaje:

```php linenums="1"
<?php
var_dump(5);       // int(5)
var_dump("Hola");  // string(4) "Hola"
```

`gettype()` y `var_dump()` son herramientas de diagnóstico; para mostrar una
salida sencilla en los ejercicios, usa `print`.

## 5. Expresiones

Una **expresión** combina valores, operadores y, cuando hace falta,
paréntesis. PHP evalúa la expresión y obtiene un resultado.

```php linenums="1"
<?php
print((10 + 5) . PHP_EOL);     // 15
print((2 + 3 * 4) . PHP_EOL);  // 14
print(((2 + 3) * 4) . PHP_EOL); // 20
```

La multiplicación se calcula antes que la suma. Los paréntesis permiten
indicar qué parte se debe calcular primero. Es recomendable usarlos cuando
ayuden a que la intención quede clara.

## 6. Operaciones con números

PHP permite utilizar los siguientes operadores aritméticos:

| Operador | Operación | Ejemplo |
| --- | --- | --- |
| `+` | Suma | `5 + 3` |
| `-` | Resta | `10 - 3` |
| `*` | Multiplicación | `4 * 5` |
| `/` | División | `7 / 2` |
| `**` | Potencia | `2 ** 3` |
| `%` | Resto de la división | `10 % 3` |

Puedes mostrar directamente el resultado de cualquiera de estas expresiones:

```php linenums="1"
<?php
print((5 + 3) . PHP_EOL);   // 8
print((10 - 3) . PHP_EOL);  // 7
print((4 * 5) . PHP_EOL);   // 20
print((7 / 2) . PHP_EOL);   // 3.5
print((2 ** 3) . PHP_EOL);  // 8
print((10 % 3) . PHP_EOL);  // 1
```

### División y división entera

El operador `/` realiza una división normal. Si el resultado no es entero,
PHP devuelve un `float`. Si la división entre enteros es exacta, PHP puede
devolver un `int`.

Para obtener solo el cociente entero se utiliza `intdiv()`, no `//`:

```php linenums="1"
<?php
print((17 / 5) . PHP_EOL);         // 3.4
print(intdiv(17, 5) . PHP_EOL);    // 3
print((17 % 5) . PHP_EOL);         // 2, el resto
```

En Python, `//` representa la división entera; en PHP, `//` inicia un
comentario de una línea. No confundas ambos lenguajes.

El operador `%` obtiene el resto de la división entera. Por ejemplo, al
dividir 17 entre 5, el cociente entero es 3 y el resto es 2:

```php linenums="1"
<?php
print((15 % 4) . PHP_EOL);  // 3
print((20 % 4) . PHP_EOL);  // 0
```

## 7. Operaciones con cadenas de texto

### 7.1 Concatenar cadenas

En PHP, el operador punto (`.`) une cadenas. A esta operación se le llama
**concatenación**.

```php linenums="1"
<?php
print("Hola" . " " . "mundo" . PHP_EOL);       // Hola mundo
print("PHP" . " 8" . PHP_EOL);                 // PHP 8
print("Me gusta " . "programar" . PHP_EOL);    // Me gusta programar
```

El operador `+` es para operaciones numéricas; para unir texto, usa `.`.

### 7.2 Repetir cadenas

En PHP, el operador `*` multiplica números; no repite cadenas como ocurre en
Python. Para repetir un texto utiliza la función `str_repeat(texto, cantidad)`:

```php linenums="1"
<?php
print(str_repeat("Ja", 3) . PHP_EOL);   // JaJaJa
print(str_repeat("PHP", 2) . PHP_EOL);  // PHPPHP
print(str_repeat("* ", 5) . PHP_EOL);   // * * * * *
```

La cantidad de repeticiones se indica como un número entero.

## 8. Aplicar la teoría a los ejercicios

Para completar los ejercicios de este tema:

1. Abre el fichero `.php` con la etiqueta `<?php`.
2. Escribe una instrucción `print(...)` por cada valor o resultado que debas
   mostrar y termina cada instrucción con `;`.
3. Escribe el texto entre comillas; escribe los números sin comillas si
   quieres que sean valores numéricos.
4. Usa `+`, `-`, `*` o `/` para las operaciones indicadas. PHP respeta la
   precedencia de operadores; usa paréntesis si necesitas cambiar el orden.
5. Si tienes que mostrar varias líneas, añade `PHP_EOL` entre ellas. `print`
   no crea saltos de línea por sí solo.
6. Ejecuta el programa con `php nombre-del-fichero.php` y compara la salida
   con la solicitada.

Por ejemplo, una expresión con suma y multiplicación se escribe así:

```php linenums="1"
<?php
print(2 + 3 * 4);
```

## Resumen

- Un **literal** es un valor escrito directamente en el código.
- Los tipos básicos son `int`, `float`, `string` y `bool`.
- `print(...)` muestra un valor o el resultado de una expresión; no añade un
  salto de línea automáticamente.
- Las instrucciones PHP terminan normalmente en `;` y se ejecutan en orden.
- Los operadores numéricos incluyen `+`, `-`, `*`, `/`, `**` y `%`.
- `intdiv(a, b)` obtiene el cociente entero; `%` obtiene el resto.
- El operador `.` concatena cadenas; `str_repeat()` repite una cadena.
- `gettype()` permite consultar el tipo de un valor.