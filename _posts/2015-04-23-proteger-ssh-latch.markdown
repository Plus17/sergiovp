---
title: 'Proteger el SSH con Latch'
date: 2015-04-23 00:00:00
tags: latch seguridad
layout: post
---
En este manual les mostrare como instalar y configurar la herramienta Latch de ElevenPaths para mejorar la seguridad de nuestro servidor GNU/Linux, protegeremos el acceso vía **SSH**.

Basicamente Latch se define como un "cerrojo" para nuestras **identidades virtuales**, puedes incluirlo en muchisimos sitios mediante plugins listos para usar (Wordpress, owncloud, joomla, prestashop, etc), en sitios donde ya esta implementada la funcion como Tuenti o mediante el SDK que ponen a disposición de los desarrolladores en multiples lenguajes.

Con esta herramienta podemos agregar un **segundo factor de autenticación** o **2FA**, basicamente el servicio protegido por esta herramienta solo estara disponible si tenemos el cerrojo abierto, en caso contrario sera imposible acceder a el.

Tambien puede usarse como otras aplicaciónes tipo *Google Authenticator*, con passwords de un solo uso, aúnque a mi parecer la primera opción es bastante buena y la segunda solo esta disponible en la versión pro.

Por otra parte si bien hay otros tutoriales para realizar esta misma tarea (como [este](http://www.dexa-dev.com/securizando-un-vps-con-latch-i/) o [este otro](http://www.hackplayers.com/2014/01/securizando-openssh-mode-paranoico-2.html)  , por lo que he visto se basan en un plugin anterior que era exclusivo para **SSH**, despues este se elimino y su funcionalidad se añadio a la de otros plugins en uno especifico para UNIX, por lo que con el animo de aportar me animo a realizar esta entrada.

Bien, lo primero es que *ElevenPaths* nos proporciona el latch-plugin-unix, que incorpora varias funcionalidades para sistemas UNIX, podemos proteger cuentas de usuario, SSH, FTP, etc, aunque solo lo he utilizado para la segunda opción.

El manual se divide en las siguientes partes para su mejor lectura:

1. Crear una cuenta de desarrollador y una app
2. Instalar las librerias necesarias
3. Instalación y configuración del plugin
4. Configurar el plugin en SSH
5. Parear cuenta
6. Eliminar cuenta y plugin

### 1.Crear una cuenta de desarrollador:

1. Necesitamos registrarnos en la [web oficial](https://latch.elevenpaths.com) como desarrolladores: Esto nos dara acceso a las distintas herramientas necesarias:
	* Ingresar en "Área de desarroladores"
    * Clic en el botón "Reistrarse como desarrollador"
2. Crear una nueva aplicación: Una vez registrados, accedemos al area para desarrolladores, creamos una nueva app, le damos un nombre
![Crear app latch](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_262/v1428901247/crear_app_latch_lsprmo.png)
3. Por ultimo necesitamos guardar dos datos importantes, el *ID de aplicación* y la *secret_key*, los datos que se muestran en la imagen.
![Datos necesarios para latch](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_255/v1428901249/Datos_de_la_app_unvvbb.png)

* Aqui tenemos otra opción:
	* Podemos crear "operaciones", para poder controlar acciones por separado, como comente con este plugin podemos proteger tanto el servicio SSH, como las cuentas de usuario, el comando sudo, etc, creando acciones podemos manejarlas de manera separada.
		* Sin embargo estas no son obligatorias, en caso de querer hacer una para el ssh en especifico seria de la siguiente manera:
	* Creamos una operacion llamada "sshd-login" con OTP y LOR desabilitados. Al añadir la operación se creara un id que aparece junto al nombre entre [], tambien lo necesitaremos.

3. Instalar la app de latch para Android, IOS o Windows Phone dependiendo de donde la utilizaremos, desde ahí controlaremos los "cerrojos".
![Latch para Android](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_302/v1428901250/latch_android_jyd5in.jpg)
4. Iniciar Sesión en la aplicación movil utilizando los datos con los que nos registramos en la web de la herramienta.

### 2. Librerias necesarias:

Ahora ingresamos a nuestro servidor, antes de comenzar todo el proceso es necesario satisfacer algunas dependencias del plugin, librerias necesarias para su funcionamiento, para ello utilizaremos los siguientes comandos:

`sudo apt-get install gcc make`

`sudo apt-get install libpam0g-dev libcurl4-openssl-dev libssl-dev`

### 3. Instalación y configuración del plugin:

1. Necesitaremos descargar el plugin desde https://github.com/ElevenPaths/latch-plugin-unix.
	* Es necesario tener instalado git en el servidor, en caso de no tenerlo ejecutar: `sudo apt-get install git`

2. Ahora clonamos el repositorio:
`git clone https://github.com/ElevenPaths/latch-plugin-unix.git`
3. Ingresamos a la carpeta donde se descargo el proyecto:
`cd latch-plugin-unix`
4. Ejecutamos el siguiente comando para instalar:
`./configure prefix=/usr sysconfdir=/etc && make && sudo make install`

5. Editamos el archivo de configuración donde agregaremos la información que obtuvimos al crear la app en la web de Latch, el  "Application ID" y "SecretKey" con el comando:

`sudo nano /etc/latch/latch.conf`

Tendremos el siguiente contenido en la terminal:

```language-bash
# Identify your Application
# Application ID value
#
app_id = REPLACE_APP_ID_HERE

# Secret key value
#
secret_key = REPLACE_SECRET_KEY_HERE
```

Solo hace falta reemplazar los valores correspondientes.

Opcional:

* Si se creo la operacion *sshd-login* en la sección #Operations, buscamos la linea:
* 'sshd-login = REPLACE_OPERATION_ID_HERE'
* Reemplazamos el texto "REPLACE_OPERATION_ID_HERE" por el id que obtuvimos al crear la operación.

Una vez editados los campos, "Ctrl + X" para cerrar, "Y" para confirmar los cambios y un ultimo "Enter" para guardar el archivo.

6. Ahora es necesario mover el archivo `pam_latch.so` con el comando:
`sudo mv /usr/lib/pam_latch.so /lib*/*/security`

### 4. Configurar el plugin en el SSH:

1. Editamos el siguiente archivo:
`sudo nano /etc/pam.d/sshd`

2. Debajo de `@include common-auth`, agregamos la siguiente linea:
`auth	   required	pam_latch.so config=/etc/latch/latch.conf accounts=/etc/latch/latch.accounts operation=sshd-login otp=no`.

	* Las opciones `operation=sshd-login otp=no`, solo se agregaran en caso de haber configurado la acción *sshd-login*, en caso contrario las omitimos.

3. Editamos el archivo sshd_config mediante:
`sudo nano /etc/ssh/sshd_config `

	* Buscamos la linea `ChallengeResponseAuthentication`, la cual debe quedar así:
`ChallengeResponseAuthentication yes`

	* Buscamos `PasswordAuthentication` y debe quedar:
`PasswordAuthentication no`

	* Nos aseguramos que al final del archivo este la linea:
`UsePAM yes`

	* Si alguna linea esta comentada con `#` al inicio, se le quita y se cambia el valor al que se menciona.

4. Ahora reiniciamos el servidor SSH:

`sudo service ssh restart`

### 5. El ultimo paso es parear la cuenta:

Para esto son necesarios los siguientes dos pasos, ya con la aplicación instalada en nuestro movil:

1. En el móvil:
	* Abrir la aplicación
	* Usamos la opción agregar un nuevo servicio y luego generar nuevo codigo.
![Agregar un nuevo servicio](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_331/v1428901249/generar_codigo_wtr078.jpg)
2. En la terminal con la sesión de servidor ejecutamos el comando:

`latch -p CÓDIGO`

* CÓDIGO es el que nos proporciona la app móvil.

Nos devolvera el mensaje:

`Account successfully paired to the user USUARIO`

Además deberia saltar una alerta en el movil, informando sobre la acción, elegimos la opcion "setup now", elegimos si quieremos utilizar alguna de las opciones y listo.

####Ahora solo resta probar si todo funciona correctamente:
Si el servicio esta desbloqueado y accedemos recibiremos una alerta como la siguiente:

![Acceso con latch abierto](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_360/v1428901249/cerrojo_abierto_ulzekd.jpg)

En cambio si esta bloqueado y damos los datos correctos de acceso sera como la siguiente:

![Acceso con latch cerrado](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_360/v1428901249/cerrojo_cerrado_q0hc5p.jpg)

Y veremos que no nos permite acceder.

Recordemos que latch no avisa de intentos de acceso, solo cuando se produce un login exitoso, es decir con los datos correctos.

### 6. Dejar de usar latch:

Podemos desemparejar una cuenta con el comando:
`latch -u`

Esta acción arrojara el mensaje:
`Account belonging to the user USUARIO successfully unpaired from latch`

* Para desinstalar el plugin se deben revertir los cambios realizados en los ficheros, acceder a la carpeta del plugin y ejecutar el siguiente comando:
`./configure prefix=/usr sysconfdir=/etc && make && sudo make uninstall`

Para información adicional:
No pueden perderse la serie de entradas en el blog de El Maligno: [Latch: Cómo proteger las identidades digitales](http://www.elladodelmal.com/2013/12/como-proteger-las-identidades-digitales.html)
No dejen de revisar el GitHub donde encontraran muchisimo codigo he información: https://github.com/ElevenPaths
En diversos blogs hay tutoriales para utilizar Latch en diversos sitios, en el mismo blog oficial o en elladodelmal hay muchos post con información muy valiosa sobre esta herramienta.
