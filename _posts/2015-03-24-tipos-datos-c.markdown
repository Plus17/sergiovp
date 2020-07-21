---
title: 'Tipos de datos en C'
date: 2015-03-24 00:00:00 
tags: c
layout: post
---
Tutorial de C: Tipos de datos

Ya, ya se, dirán: "¿Mas párrafos de teoría?, puff hay que escribir código", ciertamente puede ser aburrido, pero las bases son importantes, conforme avancemos estos conceptos se irán obviando, pero es lo básico que hay que saber y sin las bases sera mas difícil avanzar, así que empecemos.

Es importante conocer que en C, como en la mayoría de lenguajes de programación hay diversos tipos de datos (en unos mas en otros menos), los básicos son:

* int - Numero entero, ejemplo: 10.
* float - Numero real, ejemplo: 3.1416.
* char - Caracter, ejemplo: "a".


La forma de declararlos es la siguiente:

```language-c

int main(){

	int numero = 5;
	float numero_flotante = 3.1415;
	char caracter[5]="Hola"; // aquí especificamos la longitud, que sera hasta 5 caracteres
	

} // todas las funciones en C se delimitan mediante { y }
```

Existen otros, como: double, long int, etc, etc. De estos no es necesario  preocuparse por ahora.

Hay que decir que C es un lenguaje "fuertemente tipado", es decir que cuando declaremos variables debemos especificar explícitamente su tipo y se deben utilizar solo como se espera que sean usadas, no es como en PHP donde podemos utilizar las variables a diestra y siniestra.

Existen casos donde el compilador se encarga de realizar conversiones, por ejemplo si decimos que utilizaremos una variable con un numero real pero en realidad la que usamos es de tipo entero, no habría problema, se realizaría de forma implícita, pero si son incompatibles, como float (numero real) y char (caracter), se producirá un error.

Por supuesto podríamos hacer conversiones de un tipo a otro, el llamado casting.
