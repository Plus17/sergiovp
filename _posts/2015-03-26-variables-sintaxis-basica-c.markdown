---
title: 'Variables y sintaxis básica en C'
date: 2015-03-26 00:00:00
tags: c
layout: post
---
Las **variables** son espacios en memoria a los que podemos asignar valores de distintos tipos, se declaran de la siguiente forma  	`[tipo] [variable]`, por ejemplo:
```language-c
int numero=0;
string nombre="MiBlog"; // Las cadenas de caracteres deben ir entre comillas dobles
char caracter[5]="HOLA";
```
Lo que estamos declarando es una variable de tipo entero (int), es decir números sin decimales como vimos el la primer parte [Tipos de datos en C](https://sergiovp.dev/tipos-de-datos-en/), recordemos que en C debemos indicar explícitamente el tipo de variable a utilizar.

En el caso del tipo char debemos definir una longitud máxima entre corchetes, es decir el máximo de caracteres que contendrá la variable.

Otro aspecto importante es conocer el **ámbito** de las variables, ya que estas pueden ser globales o locales, las variables globales puedes ser accedidas desde cualquier parte de nuestro programa, en cambio las variables locales solo pueden ser usadas en donde se declararon, por ejemplo una función:
```language-c
int global; // esta es una variable global
int main(){

	int local; // esta es una variable local
    //y solo pude ser usada en mifuncion
	return 0;

}
```
Es tiempo de hablar de la sintaxis básica, de la cual ya vimos un poco en las lineas anteriores, C es estricto con su sintaxis, analicemos el ejemplo del primer:
```language-c
#include <stdio.h>

int main(){

	printf("Hola Mundo!!");
	return 0;

}
```
`#include` : se usa para "incluir" librerías (archivos que contienen funciones), en el ejemplo se incluye la librería standar stdio.h que contienen funciones básicas como printf y scanf.

`int main()` : aquí definimos una función, las funciones son una serie de instrucciones agrupadas, int hace referencia a que se espera que la función devuelva un resultado de tipo entero, main sirve para definir la función principal, por lo que sera la primera en ejecutarse, además, todas las funciones llevan paréntesis ().

Entre los paréntesis podríamos definir argumentos para la función, que básicamente serian variables necesarias para que esta realice su tarea, un ejemplo seria: int funcion(float pi).

Además, para delimitar las instrucciones de una función estas deben estar dentro de los signos { y }, estos delimitan el inicio y el final de la función.

`printf("Hola Mundo!!");`, es una función estándar, esta tiene varios elementos:
printf Esta es una palabra reservada, es decir que es utilizada por el lenguaje y no puede ser usada para otros propósitos como definir variables.

`("");` Como vemos si queremos imprimir texto este debe ir entre comillas dobles, y todas las sentencias deben terminar con punto y coma (;).

Esto es todo por esta entrada, a partir de aquí, otros conceptos sobre la sintaxis u otros aspectos se irán explicando en base a ejemplos (Vaya, por fin!!) que utilicemos en los siguientes temas.
