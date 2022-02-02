---
author: Me
title: Operadores en C
date: 2015-03-25
description: Operadores en C
math: true
ShowBreadCrumbs: false
categories: ["c"]
---

Es el turno de aprender operadores, sirven para hacer un montón de cosas en muchos lenguajes, principalmente tenemos 4 tipos de operadores:

###1. De asignación

Con este "asignamos" valores, por ejemplo si queremos asignar a la variable 'numero' un valor entero de 10, se hace de la siguiente forma:

`int a = 10;`

O podemos asignar el valor de otras variables, la variable 'f' tendra el valor de otra variable 'i' (recordar que C es fuertemente tipado, por lo que deben ser del mismo tipo):

`int i=0;`
`int f=i;`

###2. Aritmeticos

No requieren de mayor explicación, permiten realizar operaciones aritméticas (suma, resta, multiplicación, división y modulo[^1] ), en orden:

* `+, ejemplo: 5+5`
* `-, ejemplo: 3-2`
* `*, ejemplo: 6*2 (no se usa "x" para multiplicar)`
* `/, ejemplo: 10/2`
* `%, ejemplo: 26%2`

[^1]: El modulo(%) nos permite saber el residuo de una división, por ejemplo, si quisiéramos saber si un numero es divisible entre dos, utilizamos 10%2. si el resultado (o residuo), es 0, el numero es divisible entre dos (o par).

###3. De comparación

Nos permiten comparar dos expresiones, si la comparación es verdadera se devuelve un valor booleano 'true', en caso contrario sera 'false':

* `== igual que`
* `!= diferente de`
* `> mayor que`
* `< menor que`
* `>= mayor o igual que`
* `<= menor o igual que`

###4. Lógicos

Relacionan expresiones lógicas y devuelven 'true' o 'false' de acuerdo al resultado.

* `&& (AND), ejemplo: 7>5 && 8>3`
(7 mayor que 5 y 8 mayor que 3, el resultado seria true ya que ambas son ciertas).

* `|| (OR), ejemplo: 10==5 || 20!= 6`
(10 igual que 5 o 20 diferente de 6, el resultado es true ya que 20 es diferente de 6 aunque 10 no sea igual a 5).

Como vemos, si usamos `&&` devolverá true solo si todas las expresiones son ciertas, en cambio `||` devolverá true si al menos una de las expresiones es cierta. Veremos su utilidad en algunos ejemplos mas adelante.

Espero este quedando claro el tema, ya que estos operadores se utilizan de manera constante cuando programamos, de cualquier forma estos conceptos básicos lo repasaremos de forma practica mas adelante.
