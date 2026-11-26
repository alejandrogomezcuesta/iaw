# Ejercicios: condiciones en PHP

Resuelve los ejercicios con PHP. Guarda cada programa en un fichero `.php` y
ejecútalo desde la terminal integrada de Visual Studio Code, abierta en la
carpeta del proyecto:

```bash
php ejercicio1.php
```

Recuerda que las variables empiezan por `$`, las condiciones se escriben
entre paréntesis, los bloques usan llaves (`{}`) y las instrucciones terminan
con `;`. Puedes repasar comparaciones, `if`, `else`, el operador `%` y el
formato de cantidades en [Condiciones en PHP](06-condiciones-teoria.md).

## Ejercicios con `if` (sin `else`)

### Ejercicio 1: Número mayor que cero

Comprueba si un número es mayor que cero. Si lo es, muestra un mensaje
indicando que el número es positivo.

```php linenums="1"
<?php
$numero = 15;
// Escribe aquí la condición y el mensaje.
```

### Ejercicio 2: Palabra de bienvenida

Comprueba si una palabra es igual a `"hola"`. Si lo es, muestra
`"¡Hola a ti también!"`.

```php linenums="1"
<?php
$palabra = "hola";
// Escribe aquí la condición y el mensaje.
```

### Ejercicio 3: Edad para votar

Comprueba si una persona tiene 18 años o más. Si es así, muestra un mensaje
indicando que puede votar.

```php linenums="1"
<?php
$edad = 20;
// Escribe aquí la condición y el mensaje.
```

### Ejercicio 4: Nota aprobada

Comprueba si una nota es igual o superior a `5.0`. Si es así, muestra
`"¡Has aprobado!"` junto con la nota, formateada con dos decimales mediante
la función `number_format()`.

```php linenums="1"
<?php
$nota = 6.5;
// Escribe aquí la condición.
// Muestra el mensaje y la nota formateada con number_format().
```

## Ejercicios con `if` / `else`

### Ejercicio 5: Número par o impar

Comprueba si un número es par o impar. Si es par, muestra
`"El número es par"`; en caso contrario, muestra `"El número es impar"`.

Recuerda: si el resto de dividir un número entre `2` es `0`, es par. Puedes
obtener el resto con el operador `%`.

```php linenums="1"
<?php
$numero = 7;
// Escribe aquí la condición y los dos mensajes.
```

### Ejercicio 6: Temperatura

Comprueba la temperatura. Si es mayor que `25` grados, muestra
`"Hace calor"`; si no, muestra `"No hace mucho calor"`. En ambos mensajes,
incluye la temperatura formateada con un decimal usando la función
`number_format()`.

```php linenums="1"
<?php
$temperatura = 28.5;
// Escribe aquí la condición y los dos mensajes con la temperatura formateada.
```

### Ejercicio 7: Usuario registrado

Comprueba si el nombre de usuario es `"admin"`. Si coincide, muestra
`"Bienvenido, administrador"`; si no, muestra `"Bienvenido, usuario"`.

```php linenums="1"
<?php
$usuario = "juan";
// Escribe aquí la condición y los dos mensajes.
```

### Ejercicio 8: Descuento por compra

Comprueba si el importe de una compra es mayor que `100` euros. Si lo es,
calcula y muestra el importe con un descuento del 10 %. Si no, muestra el
importe original. En ambos casos, muestra el importe con dos decimales,
utilizando `number_format()`.

```php linenums="1"
<?php
$monto = 150.0;
// Escribe aquí la condición.
// Calcula o conserva el importe según corresponda y muéstralo.
```

### Ejercicio 9: Acceso al sistema

Comprueba si la contraseña coincide con `"secreto123"`. Si es correcta,
muestra `"Acceso permitido"`; si no, muestra `"Acceso denegado"`.

```php linenums="1"
<?php
$contrasena = "secreto123";
// Escribe aquí la condición y los dos mensajes.
```

### Ejercicio 10: Calificación de desempeño

Comprueba una puntuación. Si es igual o superior a `8`, muestra
`"Excelente desempeño"`; si no, muestra `"Continúa mejorando"`. En ambos
mensajes, incluye la puntuación formateada con dos decimales usando la función
`number_format()`.

```php linenums="1"
<?php
$puntuacion = 7.5;
// Escribe aquí la condición y los dos mensajes con la puntuación formateada.
```

### Ejercicio 11: Validación de cantidad

Comprueba si la cantidad de productos es mayor que cero. Si lo es, muestra
`"Hay stock disponible"`; si no, muestra `"Sin stock"`.

```php linenums="1"
<?php
$cantidad = 0;
// Escribe aquí la condición y los dos mensajes.
```

### Ejercicio 12: Tipo de suscripción

Comprueba si el tipo de suscripción es `"premium"`. Si coincide, muestra
`"Tienes acceso a todas las características"`; si no, muestra
`"Actualiza tu suscripción para más características"`.

```php linenums="1"
<?php
$suscripcion = "basica";
// Escribe aquí la condición y los dos mensajes.
```

## Ejercicios de clasificación por rangos

En los siguientes ejercicios, utiliza una cadena de condiciones con
`if`, `elseif` y `else`. Ordena los límites de menor a mayor y comprueba que
cada posible valor pertenece a un único tramo.

### Ejercicio 13: Clasificación de temperaturas

Pide o asigna una temperatura y muestra su categoría:

- Menor que `0`: bajo cero.
- Desde `0` hasta menos de `15`: frío.
- Desde `15` hasta menos de `25`: templado.
- `25` o más: caluroso.

```php linenums="1"
<?php
$temperatura = 18.5;
// Clasifica la temperatura usando if, elseif y else.
```

### Ejercicio 14: Tramos de calificación

La nota está comprendida entre `0` y `10`. Clasifícala según estos tramos:

- Menor que `5`: suspenso.
- Desde `5` hasta menos de `7`: aprobado.
- Desde `7` hasta menos de `9`: notable.
- `9` o más: sobresaliente.

```php linenums="1"
<?php
$nota = 8.5;
// Clasifica la nota usando if, elseif y else.
```

### Ejercicio 15: Coste de envío según el importe

Calcula el coste de envío según el importe de la compra:

- Menos de `20 €`: coste de envío de `4,99 €`.
- Desde `20 €` hasta menos de `50 €`: coste de envío de `2,99 €`.
- `50 €` o más: envío gratuito.

Muestra el coste de envío que corresponde al importe.

```php linenums="1"
<?php
$importe = 34.90;
// Determina el coste de envío usando if, elseif y else.
```

## Repaso

En estos ejercicios practicarás:

- Comparar números y cadenas de texto.
- Usar condiciones `if`, `elseif` y `else`.
- Comprobar si un número es par mediante el resto de la división.
- Calcular y mostrar un descuento según el importe de una compra.
- Formatear notas, temperaturas, importes y puntuaciones con `number_format()`.
- Decidir si se permite el acceso o si hay disponibilidad de productos.
- Clasificar valores en varios tramos sin dejar huecos ni solapamientos.
