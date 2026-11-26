# Ejercicios: variables y expresiones en PHP

Resuelve los ejercicios con PHP. Guarda cada programa en un fichero `.php` y
ejecútalo desde la terminal integrada de Visual Studio Code, abierta en la
carpeta del proyecto:

```bash
php ejercicio1.php
```

Recuerda que en PHP los nombres de las variables comienzan por `$`, las
instrucciones terminan con `;` y `print` muestra los resultados. Para añadir
un salto de línea puedes concatenar `PHP_EOL` con `.`. Consulta
[Variables y expresiones en PHP](04-variables-expresiones-teoria.md) si
necesitas repasar la sintaxis o las fórmulas.

## Ejercicio 1: Tu nombre y edad

Crea una variable `$nombre` con tu nombre (texto) y otra llamada `$edad` con
tu edad (número entero). Muestra ambas en la consola, cada una con una
etiqueta descriptiva.

```php linenums="1"
<?php
// Declara $nombre y $edad.
// Muestra el nombre y la edad.
```

## Ejercicio 2: Operaciones básicas

Crea dos variables numéricas. Calcula y muestra la suma, la resta, la
multiplicación y la división de esos valores. Guarda cada resultado en una
variable distinta antes de mostrarlo.

```php linenums="1"
<?php
// Declara dos variables numéricas.
// Calcula suma, resta, multiplicación y división.
// Muestra los cuatro resultados.
```

## Ejercicio 3: Conversión de euros a dólares

Crea una variable con una cantidad en euros. Para este ejercicio, usa un tipo
de cambio fijo de **1 euro = 1,08 dólares** (es un valor de ejemplo, no una
cotización actual). Convierte la cantidad a dólares con la fórmula:

```text
dólares = euros × 1,08
```

Guarda el resultado, redondeado a dos decimales, en otra variable y muéstralo.

```php linenums="1"
<?php
// Declara una cantidad en euros.
// Calcula su valor en dólares con el tipo de cambio indicado.
// Muestra el resultado con dos decimales.
```

## Ejercicio 4: Precio con IVA

Crea una variable con el precio de un producto antes de impuestos. Calcula el
IVA del 21 % y el precio final con IVA incluido. Muestra ambos resultados.
Puedes utilizar `round($cantidad, 2)` para redondear las cantidades a dos
decimales.

```php linenums="1"
<?php
// Declara el precio sin IVA.
// Calcula el IVA (21 %) y el precio final.
// Muestra ambos resultados.
```

## Ejercicio 5: Área de un rectángulo

Crea las variables `$ancho` y `$alto` con las dimensiones decimales de un
rectángulo. Calcula el área multiplicando el ancho por el alto y muestra el
resultado.

```php linenums="1"
<?php
// Declara el ancho y el alto.
// Calcula el área.
// Muestra el resultado.
```

## Ejercicio 6: Promedio de calificaciones

Crea tres variables con calificaciones (números enteros o decimales).
Calcula el promedio y muestra el resultado. Recuerda sumar las tres
calificaciones y dividir el total entre tres.

```php linenums="1"
<?php
// Declara tres calificaciones.
// Calcula el promedio.
// Muestra el resultado.
```

## Ejercicio 7: Incremento de una variable

Crea una variable con el valor `10`. Incrementa su valor en `5` usando una
asignación. Después, incrementa el nuevo valor en `3` y muestra el valor
final. Puedes escribir las actualizaciones como `$valor = $valor + 5` o
utilizar el operador abreviado `$valor += 5`.

```php linenums="1"
<?php
// Declara una variable con valor 10.
// Incrementa en 5 y después en 3.
// Muestra el valor final.
```

## Ejercicio 8: Cálculo del cambio

Crea una variable con el precio de un artículo y otra con la cantidad de
dinero entregada para pagar. Calcula el cambio restando el precio al dinero
entregado y muestra el resultado.

```php linenums="1"
<?php
// Declara el precio y el dinero entregado.
// Calcula y muestra el cambio.
```

## Ejercicio 9: Distancia y velocidad

Crea una variable con una distancia en kilómetros y otra con el tiempo
empleado en horas. Calcula la velocidad media dividiendo la distancia entre
el tiempo y muestra el resultado en kilómetros por hora. Utiliza un tiempo
mayor que cero.

```php linenums="1"
<?php
// Declara la distancia y el tiempo.
// Calcula y muestra la velocidad media.
```

## Ejercicio 10: Perímetro de un círculo

Crea una variable con el radio de un círculo (puede ser decimal). Calcula el
perímetro con la fórmula `P = 2 × π × r`, usando `3.14159` como aproximación
de π, y muestra el resultado.

```php linenums="1"
<?php
// Declara el radio y una variable $pi con el valor 3.14159.
// Calcula el perímetro y muestra el resultado.
```

## Repaso

En estos ejercicios practicarás:

- Crear variables de texto y numéricas, y reasignar sus valores.
- Mostrar variables y resultados con `print`.
- Usar suma, resta, multiplicación y división en expresiones.
- Aplicar fórmulas de conversión, porcentajes, áreas, promedios y perímetros.
- Guardar resultados intermedios en nuevas variables.
