# Condiciones en PHP

Las condiciones permiten que un programa tome decisiones y ejecute distintas
instrucciones según los datos. Por ejemplo, un juego puede reducir las vidas
del jugador si toca a un enemigo, o una aplicación del tiempo puede mostrar
un aviso cuando hace mucho calor.

## 1. Comparaciones y valores booleanos

Una comparación comprueba una relación entre valores. Su resultado es un
booleano: `true` (verdadero) o `false` (falso).

```php linenums="1"
<?php
var_dump(5 > 3);       // bool(true)
var_dump(10 == 10);    // bool(true)
var_dump("a" == "b");  // bool(false)
```

`var_dump()` muestra el valor y su tipo, por lo que resulta útil para observar
los resultados booleanos. En PHP, los valores booleanos se escriben
`true` y `false`, en minúsculas.

## 2. Operadores de comparación

Los operadores de comparación más habituales son:

| Operador | Significado |
| --- | --- |
| `==` | Igualdad de valores |
| `!=` | Desigualdad de valores |
| `<` | Menor que |
| `>` | Mayor que |
| `<=` | Menor o igual que |
| `>=` | Mayor o igual que |

Ejemplo con una variable:

```php linenums="1"
<?php
$edad = 14;

var_dump($edad == 14); // bool(true)
var_dump($edad != 14); // bool(false)
var_dump($edad < 18);  // bool(true)
var_dump($edad > 18);  // bool(false)
var_dump($edad <= 14); // bool(true)
var_dump($edad >= 18); // bool(false)
```

Para comparar si dos valores tienen el mismo valor **y** el mismo tipo, PHP
también ofrece `===` (idéntico) y `!==` (no idéntico). Por ejemplo,
`5 == "5"` es verdadero porque `==` puede convertir tipos, mientras que
`5 === "5"` es falso porque uno es entero y el otro texto. Para comparar
cadenas de texto, como nombres de usuario o contraseñas, es preferible usar
`===`.

## 3. La sentencia `if`

`if` ejecuta un bloque de instrucciones solo cuando su condición es verdadera.
En PHP, la condición se escribe entre paréntesis y el bloque entre llaves
(`{}`). Cada instrucción dentro del bloque termina con punto y coma (`;`).

```php linenums="1"
<?php
$edad = 16;

if ($edad >= 16) {
    print("¡Puedes conducir!" . PHP_EOL);
}

print("Fin del programa" . PHP_EOL);
```

Con `$edad = 16` se muestran los dos mensajes. Si cambias la edad a `14`, el
mensaje de conducción no se muestra, pero `Fin del programa` sí, porque está
fuera del bloque `if`.

Otro ejemplo:

```php linenums="1"
<?php
$puntos = 85;

if ($puntos > 80) {
    print("¡Excelente!" . PHP_EOL);
}

print("Gracias por jugar" . PHP_EOL);
```

PHP utiliza las llaves para delimitar el bloque. La indentación ayuda a leer
el código, pero, a diferencia de Python, no define qué instrucciones
pertenecen al `if`.

## 4. La estructura `if` / `else`

Si quieres ejecutar una acción cuando la condición sea verdadera y otra
cuando sea falsa, añade un bloque `else`. Se ejecuta uno de los dos bloques:

```php linenums="1"
<?php
$edad = 14;

if ($edad >= 18) {
    print("Eres mayor de edad" . PHP_EOL);
} else {
    print("Eres menor de edad" . PHP_EOL);
}
```

El mismo patrón sirve para comprobar una calificación:

```php linenums="1"
<?php
$nota = 55;

if ($nota >= 50) {
    print("¡Aprobado!" . PHP_EOL);
} else {
    print("Suspendido. Necesitas estudiar más." . PHP_EOL);
}

print("Fin de la evaluación" . PHP_EOL);
```

`Fin de la evaluación` se muestra siempre porque está después de los dos
bloques.

## 5. Condiciones por tramos con `if`, `elseif` y `else`

Cuando un valor puede pertenecer a varios tramos, encadena condiciones con
`elseif`. PHP no tiene la palabra clave `elif` de Python: se escribe
`elseif` (una sola palabra). `else` recoge los valores que no cumplieron
ninguna de las condiciones anteriores.

Por ejemplo, para clasificar temperaturas:

```php linenums="1"
<?php
$temperatura = 18;

if ($temperatura < 0) {
    print("Bajo cero" . PHP_EOL);
} elseif ($temperatura < 15) {
    print("Frío" . PHP_EOL);
} elseif ($temperatura < 25) {
    print("Templado" . PHP_EOL);
} else {
    print("Caluroso" . PHP_EOL);
}
```

Las condiciones se evalúan en orden y solo se ejecuta el primer bloque que
coincide. Por eso, después de comprobar que la temperatura es menor que `0`,
la condición `$temperatura < 15` representa implícitamente el tramo de `0`
a menos de `15`; no hace falta volver a escribir el límite inferior.

Otro ejemplo, suponiendo que la nota está entre `0` y `10`, es clasificar una
calificación:

```php linenums="1"
<?php
$nota = 8.5;

if ($nota < 5) {
    print("Suspenso" . PHP_EOL);
} elseif ($nota < 7) {
    print("Aprobado" . PHP_EOL);
} elseif ($nota < 9) {
    print("Notable" . PHP_EOL);
} else {
    print("Sobresaliente" . PHP_EOL);
}
```

Así quedan definidos los tramos: menos de `5`, desde `5` hasta menos de `7`,
desde `7` hasta menos de `9`, y desde `9` en adelante. Ordena los límites de
menor a mayor para que las condiciones no oculten tramos posteriores.

## 6. El operador módulo en una condición

El operador `%` calcula el resto de una división entera. Se puede combinar
con `if` para determinar si un número es par: un número es par si el resto
al dividirlo entre `2` es `0`.

```php linenums="1"
<?php
$numero = 10;
$resto = $numero % 2;

if ($resto == 0) {
    print("El número es par" . PHP_EOL);
} else {
    print("El número es impar" . PHP_EOL);
}
```

## 7. Mostrar números con dos decimales

`number_format()` permite mostrar una cantidad con un número determinado de
decimales. En este ejemplo se muestran dos decimales, una coma decimal y un
punto para separar millares:

```php linenums="1"
<?php
$monto = 1234.5;
print(number_format($monto, 2, ',', '.') . " €" . PHP_EOL); // 1.234,50 €
```

Para calcular un descuento del 10 %, multiplica el importe por `0.10` y
réstalo al importe original:

```php
$precio = 120;
$descuento = $precio * 0.10;
$precio_final = $precio - $descuento;
```

`number_format()` resulta útil para presentar importes con y sin descuento.

## 8. Comprueba lo aprendido

1. ¿Qué resultado da `7 >= 7`? Da `true`, porque `7` es igual a `7`.
2. Si `$temperatura` vale `28`, ¿qué muestra este código? Solo muestra
   `Fin del programa`, porque `28` no es mayor que `30`.

   ```php
   $temperatura = 28;
   if ($temperatura > 30) {
       print("Hace mucho calor" . PHP_EOL);
   }
   print("Fin del programa" . PHP_EOL);
   ```

3. ¿Cuál es la diferencia entre `if` e `if` / `else`? `if` ejecuta un bloque
   cuando la condición se cumple; `if` / `else` permite ejecutar un bloque
   distinto cuando no se cumple.

## Resumen

- Una comparación produce `true` o `false`.
- Los operadores `==`, `!=`, `<`, `>`, `<=` y `>=` permiten comparar valores.
- `===` y `!==` comparan también que los valores tengan el mismo tipo.
- `if` ejecuta un bloque si su condición es verdadera.
- `else` define qué hacer cuando la condición de `if` es falsa.
- `elseif` permite comprobar varios tramos en orden; se ejecuta el primer
  bloque cuya condición se cumple.
- En PHP los bloques se escriben entre llaves y las instrucciones terminan
  con `;`.
- `%` calcula el resto y se puede usar para comprobar si un número es par.
- `number_format()` permite presentar cantidades con decimales fijos.

Estos conceptos son suficientes para resolver los ejercicios de comparaciones,
decisiones, paridad, descuentos, acceso y disponibilidad.
