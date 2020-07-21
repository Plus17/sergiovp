---
title: 'Entrada y salida de datos en  C'
date: 2015-03-31 00:00:00 
tags: c
layout: post
---
En esta entrada veremos como solicitar datos al usuario y como guardarlos cuando este los introduzca.

Básicamente necesitaremos la librería `stdio.h`, incluirla nos permitirá utilizar la función `printf();` que sirve para mostrar texto en pantalla y `scanf();` que nos permite leer datos introducidos mediante el teclado.

Antes debemos recordar que al ser un lenguaje estricto con los tipos de datos, deberemos tratar cada dato de acuerdo a su tipo, para realizar una entrada simple de datos tendríamos el siguiente código:
```language-c
#include <stdio.h>

int main()
{
	int numero;
	printf("Introduce un numero \n");
	scanf("%d",&numero);
	printf("El numero es %d",numero);

	return 0;
}
```

Como vemos, primero declaramos una **variable** de tipo Entero(*int*) llamada numero, despues imprimimos el texto "Introduce un numero" seguida de `\n` (esto nos indica un salto de linea).

En la siguiente linea leemos el dato, scanf tiene la siguiente estructura: `scanf("%x",&variable)`, donde `x` corresponde a una letra de acuerdo al tipo de dato que se debe recibir, d para int, c para char, etc. Siempre entre comillas dobles, una coma, el signo "&" (ampersand) junto al nombre de la variable.

Después volvemos a usar la función printf, pero ahora imprime también una variable, para esto se sigue un criterio similar, con `%d`, para indicar el tipo de variable a imprimir (Esto dentro de las comillas dobles), despues de la coma el nombre de la variable, esta vez sin el sigo `&``.

Si imprimieramos mas variables en una sola linea solo basta agregar un `%d` por cada variable que se desea imprimir y donde se mostrara, de igual forma después de la coma irán los nombre de las variables (separadas por comas) en el orden en el cual aparecerán, recuerda que % debe ir seguido de la letra correspondiente al tipo de variable.

Terminaremos con el siguiente ejemplo:
```language-c
#include <stdio.h>

int main()
{
	int numero, numero2;
	printf("Introduce un numero \n");
	scanf("%d",&numero);
	printf("Introduce un segundo numero \n");
	scanf("%d",&numero2);
	printf("Los numeros son %d y %d \n",numero, numero2);

	return 0;
}
```
En el próximo post veremos uno o dos ejercicios simples para practicar lo visto hasta ahora y desarrollar un poco la soltura de la sintaxis. Si tienes dudas no dudes en comentarlo.

