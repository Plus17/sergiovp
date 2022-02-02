---
author: Me
title: Arreglos o arrays en C
date: 2015-04-09
description: Arreglos o arrays en C
math: true
categories: ["c"]
---

Ahora aprenderemos a utilizar arreglos (arrays, vectores), por lo cual debemos conocer algunos lineamientos para poder aprovecharlos:

* Como cualquier variable debe declararse su tipo
* Solo puede albergar valores del tipo con el que fue declarado
* Debe indicarse la longitud del array al ser declarado
* Cada elemento guardado tendrá una posición en el array, posición 0, 1 o 2 etc
* La primera posición de un array es la 0, no la 1

Ahora veamos un ejemplo, crearemos un arreglo de tipo char, con una longitud de 9 espacios que contendrá el texto: "HolaMundo".
```c
#include <stdio.h>


int main()
{
	char cadena[9] = "HolaMundo";

	printf("La primera letra es %c",cadena[0]);
	printf("El texto es %s",cadena);

	return 0;
}
```
Como comentamos, se declaro un array de caracteres con una longitud de nueve elementos (La longitud va entre corchetes) y se le asigno el texto "HolaMundo" (Que al ser texto va entre comillas dobles).

En la siguiente linea se imprime la primera letra de la cadena, que seria "H", se imprime la variable de la misma forma que se explicó en entradas anteriores, en la la posición 0 (se indica entre corchetes al igual que cuando se declara).

En la penúltima linea se imprime la cadena entera, para no tener que imprimir cada posición por separado se utiliza %s, para indicar que ahí va una variable de tipo cadena (o string).

Este tipo de estructuras de datos podemos aplicarlas a distintos tipos de datos, como int o double, colecciones de datos muy útiles sin duda.

Como nota, de igual forma que declaramos arreglos unidimensionales , podríamos declararlos bidimensionales (una matriz) con la siguiente notación:
tipo de dato + nombre del array + [longitud de las filas][longitud de las columnas].
```c
int matriz[3][3];
```
Con lo anterior  tendríamos una matriz de 3x3: 3 filas y 3 columnas.

Ahora, una tarea muy común es la de recorrer un arreglo, en el caso de estos ejemplos que son muy simples recorrerlos en tarea fácil ya que conocemos de antemano su longitud, solo haría falta utilizar alguna estructura como un for (Que veremos mas adelante) para recorrer todas las posiciones:
```language-c
#include <stdio.h>


int main()
{
	char numeros[4] = { 1, 2, 3, 4 };
	int i=0;
	for (i ; i<4; i++)
	{
		printf("El numero es %d",numeros[i]);
	}

	return 0;
}
```
La salida de lo anterior seria:
1
2
3
4

No es necesario entender todo el código anterior, ya explicaremos la sentencia for, pero es útil tratar de analizar dicho código por uno mismo :).
