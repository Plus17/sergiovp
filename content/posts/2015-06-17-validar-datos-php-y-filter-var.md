---
author: Me
title: Validación de datos con PHP y filter_var()
date: 2015-06-17
description: Como validar la entrada de datos utilizando filter_var() en PHP
math: true
ShowBreadCrumbs: false
categories: ["php"]
---

Ahora que sabemos como sanear variables en PHP aprenderemos a validar datos comunes utilizando la función filter_var() y sus filtro predefinidos.

A diferencia de la entrada pasada utilizaremos los filtros de validación en lugar de los de saneamiento. Recordemos el formulario del ejemplo anterior:

```php
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Formulario sencillo</title>
    <link rel="stylesheet" href="">
</head>
<body>
    <form action="datos.php" method="POST" >

        <label> Nombre </label>
        <input type="text" name="nombre" required>

        <label> Telefono </label>
        <input type="text" name="telefono" required>

        <label> Email </label>
        <input type="text" name="email" required>

        <label> Mensaje </label>
         <textarea rows="4" cols="0" name="mensaje" required>

        </textarea>

    </form>
</body>
</html>
```
Después de utilizar filter_var() para sanear los datos podemos utilizarla para validarlos como sigue:

datos.php

```php
<?php

$nombre = $_POST['nombre'];
$telefono = $_POST['telefono'];
$email = $_POST['email'];
$mensaje = $_POST['mensaje'];

filter_var($telefono,FILTER_VALIDATE_INT);

if ( filter_var($email,FILTER_VALIDATE_EMAIL) )
{
    echo "El email es valido!";

} else {

    echo "El email es invalido";

}
```

La principal limitación es la cantidad limitada de filtros para validar. En nuestro ejemplo solo podemos validar el email y un numero entero de esta forma.

Sin embargo no nos quedaremos atrapados ya que la misma función nos permite validar utilizando expresiones regulares.

En la siguiente entrada veremos esta opción y una serie de regex para validar datos comunes.

Adicionalmente puedes ver los otros filtros disponibles (bastante útiles) en:
filters_validate
