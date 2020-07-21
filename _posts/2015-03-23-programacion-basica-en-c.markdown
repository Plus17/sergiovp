---
title: 'Programación Básica en C'
date: 2015-03-23 00:00:00
tags: c
layout: post
---
Esta es la primera entrada en forma del blog, sera el primero de una serie de post que conformaran un mmmm digamos, curso básico para **aprender a programar en C**.

Esta entrada servirá a modo de introducción y a su vez de índice para tener en un solo lugar todos los post.

Indice:
1. Introducción (este post)
2. [Tipos de datos](https://sergiovp.dev/tipos-datos-c/)
3. [Operadores](https://sergiovp.dev/operadores-en-c/)
4. [Variables y sintaxis básica](https://sergiovp.dev/variables-sintaxis-basica-c/)
5. [Entrada y salida de datos](https://sergiovp.dev/entrada-salida-datos-c/)
6. [Primer ejercicio](https://sergiovp.dev/primer-ejercicio-en-c/)
7. [Array o arreglos](https://sergiovp.dev/arreglos-o-arrays-en-c/)
8. [Control de Flujo: if y else](https://sergiovp.dev/control-de-flujo-if-y-else-c/)

###¿Porque un curso? y ¿Porque acerca de C?
Afuera hay muchos recursos para aprender a programar, no solo en C, si no en muchísimos lenguajes de programación, la intención no es hacer mas de lo mismo, el principal objetivo es, (aunque quizá no sea el mas adecuado) desempolvar mis propios conocimientos en C, ya que es el lenguaje con el que aprendí a programar y que mejor manera de hacerlo que ayudar a que otros aprendan.


####Entonces, ¿Que es C?

Un lenguaje de programación compilado, de mediano nivel(o de alto nivel si nos ponemos estrictos), con el podemos interactuar directamente con el sistema, incluso incluir lenguaje ensamblador, además ha influenciado a diversos lenguajes como java, PHP, entre otros. Para mas información: [C (lenguaje de programación)](https://es.wikipedia.org/wiki/C_%28lenguaje_de_programaci%C3%B3n%29)

¿Que necesitamos para programar en C?
Un compilador y un editor de código esencialmente, existen IDE (Entornos de desarrollo integrados) que suelen incluir estas herramientas entre muchas otras para ayudar al desarrollo.

En Windows he utilizado:

* [Orwell Dev-C++](http://orwelldevcpp.blogspot.mx/)
* [Code::Blocks](http://www.codeblocks.org)

Nada mas instalar alguno estaríamos listos para comenzar, por supuesto existen otros especializados o que podemos utilizar para este fin como Codelite, Netbeans o Eclipse.

En linux es un poco mas largo el proceso, un excelente IDE para este propósito en Geany, sin embargo este no cuenta con un compilador instalado, deberos instalarlo aparte y configurar geany para que lo use o compilar desde la terminal

Para esto instalamos geany, en ubuntu y derivadas esta en los repositorios, bastara con:

```language-bash
sudo aptitude install geany
```

Para instalar el compilador, en este caso gcc (GNU Compiler Collection)

```language-bash
sudo aptitude install gcc
```
o en su defecto build-essential en lugar de gcc, que instalara otras herramientas.


Ya deberíamos estar listos, podríamos escribir un "Hola mundo" para no quedarnos con las ganas de escribir algo :), en otro post explicaremos la sintaxis:

```language-c
#include <stdio.h>
//La librería estándar stdio.h es necesaria para utilizar la función printf
int main(){

	printf("Hola Mundo!! \n"); //Imprime en pantalla el texto Hola Mundo
	return 0; /*la función main espera el retorno de un entero (int), por lo que retornamos 0 para cumplir */

} // todas las funciones en C se delimitan mediante { y }
```
Por ultimo abrimos Geany y vamos a la opción: Editar>Preferencias>Herramientas y en el campo Terminal colocamos lo siguiente:

```
x-terminal-emulator -e "/bin/sh %c"
```

De esta forma podremos compilar directamente desde nustro IDE, en otro caso podemos hacerlo desde la terminal, suponiendo que el codigo esta en el archivo 'codigo.c' en la terminal y utilizando gcc seria:
```language-bash
gcc -o codigo codigo.c
```

Esto genera un ejecutable llamado codigo, para ejecutarlo escribimos el comando:
```
./codigo
```

![Compilar y ejecutar codigo C en Linux](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_373/v1427440628/jecucion_C_yk8uy1.png)


Hasta aquí el post, espero les sea útil, no duden en comentar su opiniones, dudas o sugerencias.
