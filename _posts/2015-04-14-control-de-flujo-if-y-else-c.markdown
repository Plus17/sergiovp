---
title: 'Control de flujo con if y else en C'
date: 2015-04-14 00:00:00
tags: c
layout: post
---
Ahora que ya sabemos lo básico de [arrays](http://sergiovp.dev/arreglos-o-arrays-en-c/), es la hora de conocer un poco sobre el **control de flujo** para nuestros programas. Esto se refiere a decidir como se desarrollara el flujo de ejecución de acuerdo a ciertas circunstancias, validaciones, etc.

Pongamos un ejemplo:
Tenemos un pequeño codigo que solicita al usuario ingresar un numero e imprimir si este es par o impar, aquí es donde utilizaríamos el control de flujo, para realizar una acción u otra de acuerdo a una validación.

Estas **"validaciones"** se realizan mediante dos sentencias, `if` y `else`, que básicamente vendría a ser, si, si no.

Para el ejemplo seria:
Si el numero es par, imprime "es par",
si no (por eliminación es impar), imprime "es impar"

Ahora `if` es una función que puede devolver dos resultados, *`true`* o *`false`*, de acuerdo al resultado de lo que preguntemos, y a continuación ejecutara las instrucciones que le indiquemos, en caso contrario ejecutara lo que le indiquemos en else, este ultimo, aunque no es obligatorio incluirlo, si es recomendable utilizarlo.

Veamos como queda en código:
```language-c line-numbers
#include <stdio.h>

int main()
{
	int numero;

	printf("Ingrese un número \n");
	scanf("%d",&numero);

	if(numero%2 == 0) // si el resultado es true ejecutara lo siguiente
	{
		printf("El numero %i es par \n",numero);
	}
	else // si no, ejecutara esto
	{
		printf("El numero %i es impar \n",numero);
	}

	return 0;
}
```
Como puedes observar, ahora estamos utilizando varias de las cosas que explicamos en las primeras entradas ademas del tema actual:

* Declaración de variables.
* Entrada y salida de datos.
* Operadores aritméticos y de comparación.
* Control de flujo.

Ahora la explicación:
1. Imprimimos un mensaje y leemos lo que el [usuario ingresa mediante el teclado](http://sergiovp.dev/entrada-salida-datos-c/), esto ya lo vimos, si no los has visto puedes visitar el post :)
2. El if (esto es lo interesante):

* Estamos realizando una operación y verificando su resultado, decimos que si el modulo de lo que contiene numero es 0, es decir si `numero%2 == 0` es verdadero, imprima el mensaje que indica que el numero es par.
	* A modo de repaso, recordemos que el operador modulo(%), devuelve el residuo de una división, por lo que si el resultado del modulo (o el residuo) de cualquier numero entre 2 es 0, significa que este es par, así es como validamos.
* En caso de ser falso imprimirá el mensaje indicando que el numero es impar.

Solo para mostrar otra forma en que podriamos interpretar el ejercicio veremos el siguiente código (espero no se se preste a confusiones, en caso de ser así omitirlo es permisible por ahora):

```language-c line-numbers
#include <stdio.h>

int main()
{
	int numero;

	printf("Ingrese un número \n");
	scanf("%d",&numero);

	if(!numero%2 == 0) // si el resultado es true ejecutara lo siguiente
	{
		printf("El numero %i es impar \n",numero);
	}
	else // si no, ejecutara esto
	{
		printf("El numero %i es par \n",numero);
	}

	return 0;
}
```
¿Cual es la diferencia?, hemos invertido la lógica utilizada, ahora decimos:
Si `numero%2 == 0`, no es cierto (Es lo que hace el agregar el signo `!` al inicio), el resultado sera true, por lo que el numero es impar, en caso contrario es par.

¿Confuso?, sin duda lo es al inicio, algunas personas prefieren hacerlo de esta forma, pero no hace falta romperse la cabeza por ahora con este ejemplo, conforme desarrollemos nuestra lógica de programación entenderemos mejor estas variaciones, el objetivo es mostrar que no existe una única forma de hacer las cosas :).

Al final concluimos que estas sentencias son sumamente útiles, no por nada siempre se incluyen en temarios básicos de programación, pero con el tiempo y la practica aprenderemos usos mas avanzados de esta sentencia, pero en esta entrada lo dejaremos aquí.
