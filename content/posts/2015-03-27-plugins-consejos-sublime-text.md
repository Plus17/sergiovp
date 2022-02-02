---
author: Me
title: Plugins y consejos útiles para Sublime Text
date: 2015-03-27
description: Plugins y consejos útiles para Sublime Text
math: true
categories: ["tooling"]
---


Seguramente mas de alguno que este interesado en los temas que trata este blog conoce **Sublime Text**, es por eso que en esta entrada quiero compartir los plugins y configuraciones que en lo personal utilizo en este excelente editor de código.

###Plugins recomendados para Sublime Text:

Uso varios, para diferentes funciones, pero están esencialmente destinados para desarrollo web, estos son:

**Bracket Highlighter:**
La función de este plugin es "sencilla" pero extremadamente útil, algo que ST debería incluir por defecto, que es resaltar el inicio y cierre de las llaves { }.

**Color Highlighter:**
Cuando realizas diseño web se suelen utilizar colores en hexadecimal, rgb, etc, Color Highlighter nos permite saber del color que se trata tan solo dando clic en el código hexadecimal, este se iluminara de acuerdo al color del que se trate.

**Color Picker:**
Nos permite utilizar la paleta de colores del sistema utilizando la combinación de teclas 'Ctrl + shift + c', con ella podremos seleccionar colores en hexadecimal desde ST

**HTML5:**
Para agregar soporte para esta versión de HTML a ST.

**EMMET:**
Un plugins con un potencial enorme, antes llamado *zend-coding*, nos permite ahorrar tiempo y trabajo cuando escribimos HTML, ya que al escribir una linea de código y pulsar tabulador podemos obtener grandes resultados, podemos pasar de esto:

`nav#navegacion>ul>li.navegacion_elemento*5>a`

a esto:
```language-html
<nav id="navegacion">
	<ul>
		<li class="navegacion_elemento"><a href=""></a></li>
		<li class="navegacion_elemento"><a href=""></a></li>
		<li class="navegacion_elemento"><a href=""></a></li>
		<li class="navegacion_elemento"><a href=""></a></li>
		<li class="navegacion_elemento"><a href=""></a></li>
	</ul>
</nav>
```
Al principio puede intimidar un poco, pero se volverá imprescindible.

**AutoFileName:**
Editores como Brackets tienen una genial característica que es mostrar los archivos que tenemos conforme escribimos rutas en el código, por ejemplo si utilizamos un 'require' en PHP, conforme escribamos la ruta para incluir nuestro archivo se irán mostrando las carpetas y archivos que estén en el mismo nivel, esto nos ahorra mucho tiempo al incluir archivos y con este plugin añadimos esto a nuestro editor.

###Configuraciones útiles:

ST es un editor sumamente personalizable, podemos modificar múltiples aspectos, para esto vamos a *Preferences>Settings - User*, ahí podemos agregar las siguientes opciones:

*Cambiar la fuente:*
Para ello añadimos la siguiente linea:

`"font_face": "SourceCodePro-Medium",`

Como verán yo estoy utilizando la fuente SourceCodePro-Medium, pero pueden utilizar cualquier fuente que este instalada en su sistema, esto resulta provechosos ya que existen fuentes mas recomendables para el código que las "comunes", con mono espaciado y otras características.

*Resaltar la linea actual:*

Solo hace falta añadir la siguiente linea:

`"highlight_line": true,`

*Resaltar las pestañas con cambios:*

Por defecto al modificar el contenido de un archivo ST señala la pestaña del mismo de una forma muy sutil, para mejorar esto agregamos:

`"highlight_modified_tabs": true,`

*Guardar los cambios al cambiar de ventana:*

Después de utilizar esta opción me resulta incomodo tener que presionar Ctrl + s cada vez que quiero guardar un cambio, mas si esto significa que iras a ver dicho cambio en el navegador, agregando la opción:

`"save_on_focus_lost": true,`

ST guardara los cambios realizados cada vez que pierda el foco, es decir cada ves que cambiemos de ventana.

*Resaltar las carpetas de los archivos en el sidebar:*

Si tienes muchos proyectos en el sidebar de ST, posiblemente se vuelva confuso el árbol de archivos, para resaltar las carpetas de los archivos agregamos la siguiente linea:

`"bold_folder_labels": true,`

Y eso es todo, con estos plugins y configuraciones tenemos un Sublime Text listo para la acción. Seguro pareciera que mi perfil es frontend, pero no!, jeje es backend.

Como comentario final quisiera hacer énfasis en una idea, que es sacarle el máximo provecho a nuestro editor, mas allá de si utilizas uno u otro, muchas veces desperdiciamos características geniales de cada editor, nos quedamos con cosas como el auto completado y nos olvidamos de características como los cursores múltiples o que en sublime text podemos ordenar alfabéticamente los atributos css seleccionándolos y presionado F9, esos shortcuts que ahorran valiosos segundos que terminan significando un incremento en nuestra productividad :).

En fin, es privilegiar nuestras necesidades y comodidad sobre la "moda" de utilizar un editor u otro, date un tiempo de explorar las diversas características que te ofrecen y elige el que mejor te acomode.
