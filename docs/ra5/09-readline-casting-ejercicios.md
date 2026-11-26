# Ejercicios: entrada de datos y conversión de tipos en PHP

Practica cómo pedir datos al usuario, convertirlos al tipo adecuado, hacer
cálculos y tomar decisiones. Los cuatro primeros ejercicios incluyen más
ayuda; a partir del ejercicio 5, resuélvelos de forma independiente.

Guarda cada programa en un fichero `.php` y ejecútalo desde la terminal
integrada de Visual Studio Code, abierta en la carpeta del proyecto:

```bash
php ejercicio1.php
```

Los programas utilizan `readline()` para pedir datos. Las respuestas llegan
como texto; conviértelas a `(int)` o `(float)` según corresponda. Puedes
consultar [Entrada de datos y conversión de tipos en PHP](08-readline-casting-teoria.md)
y los apuntes de [condiciones en PHP](06-condiciones-teoria.md).

## Ejercicio 1: Área de un rectángulo (con mucha ayuda)

Pide al usuario el largo y el ancho de un rectángulo, calcula el área y
muéstrala. El área se calcula multiplicando largo por ancho.

```php linenums="1"
<?php
// Pide el largo y conviértelo a decimal.
$largo = (float) readline("Introduce el largo del rectángulo: ");

// Pide el ancho y conviértelo a decimal.
// ESCRIBE AQUÍ LA LÍNEA QUE PIDE EL ANCHO

// Calcula el área (largo × ancho).
// ESCRIBE AQUÍ LA LÍNEA QUE CALCULA EL ÁREA

// Muestra el resultado.
// ESCRIBE AQUÍ LA LÍNEA QUE IMPRIME EL ÁREA
```

## Ejercicio 2: Área de un círculo (con ayuda media)

Pide el radio de un círculo y calcula su área con la fórmula `A = π × r²`.
Utiliza `3.14159` como aproximación de π.

```php linenums="1"
<?php
// Pide el radio y conviértelo a decimal.
$radio = (float) readline("Introduce el radio del círculo: ");
$pi = 3.14159;

// Calcula el área con A = pi * radio elevado a 2.
// ESCRIBE AQUÍ LA LÍNEA QUE CALCULA EL ÁREA

// Muestra el área calculada.
// ESCRIBE AQUÍ LA LÍNEA QUE IMPRIME EL ÁREA
```

## Ejercicio 3: Cálculo de descuento (con poca ayuda)

Pide el precio original de un producto y un porcentaje de descuento. Calcula
el importe descontado y el precio final.

```php linenums="1"
<?php
$precio_original = (float) readline("Introduce el precio original: ");
$descuento_porcentaje = (float) readline("Introduce el porcentaje de descuento: ");

// Calcula el descuento: precio original × porcentaje / 100.
// COMPLETA EL CÁLCULO DEL DESCUENTO

// Calcula el precio final: precio original - descuento.
// ESCRIBE AQUÍ LA LÍNEA QUE CALCULA EL PRECIO FINAL

// Muestra el precio final con dos decimales usando number_format().
```

## Ejercicio 4: Mayor de dos números (casi sin ayuda)

Pide al usuario dos números y determina cuál es el mayor. Si son iguales,
indícalo. Muestra el resultado usando condiciones.

```php linenums="1"
<?php
$numero1 = (float) readline("Introduce el primer número: ");
$numero2 = (float) readline("Introduce el segundo número: ");

// Usa if, elseif y else para comparar los dos números.
// ESCRIBE EL CÓDIGO AQUÍ
```

## Ejercicio 5: Calculadora de IMC

Pide el peso en kilogramos y la altura en metros. Calcula el Índice de Masa
Corporal (IMC) con la fórmula `IMC = peso / altura²` e indica si corresponde
a bajo peso (IMC < 18,5), rango normal (18,5–24,9) o sobrepeso (IMC ≥ 25).
La altura debe ser mayor que cero.

```php linenums="1"
<?php
// Escribe el programa: pide y convierte los datos, calcula el IMC
// y muestra la clasificación correspondiente.
```

## Ejercicio 6: Validador de número par o impar

Pide un número entero y determina si es par o impar. Utiliza el operador
módulo (`%`) para comprobar el resto de dividirlo entre `2`.

```php linenums="1"
<?php
// Escribe el programa: pide un entero, comprueba el resto y muestra
// si el número es par o impar.
```

## Ejercicio 7: Calculadora de factura con impuesto

Pide el precio base de una factura y calcula el IVA del 21 %. Después,
pregunta si el cliente tiene un cupón de descuento del 10 %. Si responde que
sí, aplica el descuento al total con IVA. Muestra el precio base, el IVA, el
descuento (si corresponde) y el total final.

```php linenums="1"
<?php
// Escribe el programa: pide los datos, calcula el IVA y usa una
// condición para aplicar o no el descuento.
```

## Ejercicio 8: Clasificador de edades

Pide la edad de una persona y clasifícala en una de estas categorías:

- Niño: menos de 13 años.
- Adolescente: de 13 a 17 años.
- Adulto: de 18 a 64 años.
- Jubilado: 65 años o más.

Muestra la clasificación junto con la edad.

```php linenums="1"
<?php
// Escribe el programa: pide la edad como entero y clasifícala
// con if, elseif y else.
```

## Ejercicio 9: Conversor de moneda con validación

Pide una cantidad en euros y una moneda de destino (`USD`, `GBP` o `JPY`).
Utiliza estos tipos de cambio:

- 1 EUR = 1,10 USD
- 1 EUR = 0,86 GBP
- 1 EUR = 130 JPY

Convierte la cantidad y muestra el resultado. Si la moneda no es una de las
tres indicadas, muestra un mensaje de error.

```php linenums="1"
<?php
// Escribe el programa: pide la cantidad y la moneda de destino,
// valida la moneda, realiza la conversión y muestra el resultado.
```

## Ejercicio 10: Calculadora de bonificación salarial

Pide el salario base de una persona y los años trabajados en la empresa.
Calcula la bonificación según esta escala:

| Años trabajados | Bonificación |
| --- | --- |
| Menos de 2 | 0 % |
| De 2 a 5 | 5 % |
| De 6 a 10 | 10 % |
| Más de 10 | 15 % |

Muestra el salario base, el importe de la bonificación y el salario total.

```php linenums="1"
<?php
// Escribe el programa: pide el salario y los años trabajados,
// calcula el porcentaje que corresponde y muestra el desglose.
```

## Ejercicios adicionales: clasificación por rangos

En estos ejercicios debes pedir un valor numérico y clasificarlo usando
`if`, `elseif` y `else`. Comprueba los límites de cada tramo para que ningún
valor quede sin clasificar.

### Ejercicio 11: Clasificación de un número entero

Pide un número entero y clasifícalo en uno de estos tramos:

- Menor que `0`: negativo.
- Igual a `0`: cero.
- Mayor que `0` y menor que `100`: positivo menor que cien.
- `100` o más: cien o más.

```php linenums="1"
<?php
$numero = (int) readline("Introduce un número entero: ");
// Clasifica el número usando if, elseif y else.
```

### Ejercicio 12: Consumo mensual de datos móviles

Pide el consumo mensual en gigabytes (GB) y clasifícalo:

- Menor que `0`: valor no válido.
- Desde `0` hasta menos de `2`: consumo bajo.
- Desde `2` hasta menos de `10`: consumo moderado.
- Desde `10` hasta menos de `20`: consumo alto.
- `20` o más: consumo muy alto.

```php linenums="1"
<?php
$consumo = (float) readline("Introduce el consumo mensual en GB: ");
// Clasifica el consumo usando if, elseif y else.
```

### Ejercicio 13: Clasificación del nivel de ruido

Pide un nivel de ruido en decibelios (dB) y clasifícalo:

- Menor que `30`: muy bajo.
- Desde `30` hasta menos de `60`: bajo.
- Desde `60` hasta menos de `85`: moderado.
- `85` o más: alto.

```php linenums="1"
<?php
$ruido = (float) readline("Introduce el nivel de ruido en dB: ");
// Clasifica el nivel de ruido usando if, elseif y else.
```

## Repaso

En estos ejercicios practicarás:

- Leer datos desde la terminal con `readline()`.
- Convertir entradas a enteros y decimales.
- Aplicar fórmulas con datos introducidos por el usuario.
- Usar comparaciones y condiciones para clasificar o validar datos.
- Mostrar resultados de cálculos y conversiones.
- Pedir valores numéricos y clasificarlos en tramos con `if`, `elseif` y `else`.
