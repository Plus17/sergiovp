---
author: Me
title: Como configurar un dominio en Ghost y DigitalOcean
date: 2015-04-02
description: Como configurar un dominio en Ghost y DigitalOcean
math: true
---

Como saben este blog usa **Ghost** como plataforma para publicar, la cual funciona sobre **Node.js** y ya que tuve que investgar como hacer el proceso para este blog porque no compartirlo.

La idea es que al finalizar esta entrada tengamos todo configurado para poder acceder a Ghost mediante nuestro propio .com, .net, etc y de acuerdo a nuestra preferencias, para este caso utilizando la forma sin www, de modo que al ingresar a *www.midomino.com* redireccione a *midominio.com*.

El objetivo es aprender a configurar un dominio con esta plataforma y un droplet de **DigitalOcean**, en lo personal tuve varios inconvenientes en este tema (cuestión de la inexperiencia jaja), no es tema de esta entrada explicar como instalar la plataforma ya que gracias a las herramientas que se nos proporcionan es muy facil, pero los pasos se resumen en los siguientes:

Iniciar sesión en DO y clic en "Create Droplet":

1. Seleccionar el tamaño y costo
2. Elegir una ubicación o región
3. En la opción select image serán dos pasos:
	* En la pestaña "Linux Distributions" elegimos la distribución linux a instalar, recomiendo Ubuntu o Debian
	* En la pestaña "Aplications" seleccionamos Ghost

Listo, creamos el droplet y se nos enviaran por correo los datos de acceso.

NOTA: Algunas opciones en "Settings" como "enable backups" conllevan un costo asociado, hay que elegir adecuadamente.

Ahora ya con Ghost instalado, veremos que podremos acceder mediante la ip que nos proporciona el servidor, pero nosotros queremos ingresar mediante el dominio que compramos. Sera necesario trabajar en 4 lugares:

* En la **configuración servidor (Nginx)**
* En la **configuración de Ghost**
* Donde tengamos el dominio registrado
* En DigitalOcean.

Como aviso, antes de realizar los pasos se recomienda agregar ambas formas de nuestra  dirección (con y sin www) a Google Web Master Tools y elegir el de nuestra preferencia en la configuración para efectos de SEO, más aún si se realiza una migración y ambos dominios estaban activos.

###En el servidor:

Debemos ingresar a nuestro VPS con los datos enviados a nuestro correo al momento de crear el Droplet, una vez ahí ingresamos el siguiente comando:

```language-bash
sudo nano /etc/nginx/sites-available/ghost
```

NOTA: Agrego 'sudo' ya que en este caso el usuario root esta deshabilitado, en caso dcontrario no hace falta

Ahora nos movemos en el contenido del archivo con las flechas del teclado y veremos que en **server_name** pone *my-ghost-blog.com*, a nosotros nos debe quedar de la siguiente manera:

```
  server_name midominio.com www.midominio.com;

    if ($host = 'www.midominio.com' ) {
            rewrite  ^/(.*)$  http://midominio.com/$1  permanent;
    }
```
Solo hace falta reemplazar las direcciones con las nuestras.

Ojo con usar "Ctrl + c" o "Ctrl + v", estamos en el editor nano y aqui no funcionan :).

Una vez editado pulsamos  *Ctrl + x*, nos pregunta si queremos guardar los cambios escribimos *y* , por último *enter* para confirmar la edición.

###En la configuración de Ghost

Ahora utilizamos el comando:

```language-bash
sudo nano /var/www/ghost/config.js
```
Repetimos la operación anterior cambiando 'my-ghost-blog.com' por el que tengamos y los mismos pasos para guardar.

Ahora toca reiniciar los servicios:

Para reiniciar ghost:
`service ghost status`

Para reiniciar NginX:
`/etc/init.d/nginx restart`

###En el registrador de dominios:

Aquí necesitamos cambiar los **DNS** que gestionan nuestro dominio, puede varias dependiendo del registrador, pero en NameCheap es así:

1. Vamos a la opción "Manage Domains"
2. Elegimos el dominio que configuraremos
3. En "NAME SERVER INFORMATION" usaremos la opción: "Specify Custom DNS Servers ( Your own DNS Servers )"
4. Llenamos los datos con las DNS de DO:
    * ns1.digitalocean.com
    * ns2.digitalocean.com
    * ns3.digitalocean.com
5. Save Changes y listo

 ![Cambiar DNS en NameCheap](https://res.cloudinary.com/escribocodigo/image/upload/v1428005051/Namecheap_xb9rei.png)

###En DO:

1. Después de loguearnos vamos a la opción DNS
2. Elegimos: "Add Domain"
3. En Name ponemos el dominio
4. En IP Address iría la ip que nos corresponde, tenemos un menú desplegable donde elegimos el droplet que creamos y este dato se llena automáticamente.

 ![Apuntar dominio en DigitalOcean](https://res.cloudinary.com/escribocodigo/image/upload/c_scale,h_366/v1428005051/Agregar_dominio_do_euh9by.png)

Ahora veremos que tenemos un registro de tipo A creado.

Nosotros agregaremos un nuevo registro de tipo CNAME:
1. Clic en "Add Record" y elegir CNAME
2. En Name ponemos: www
3. En Hostname va: @
4. Clic en Create.

Listo, ahora llevara un tiempo para que los DNS se propaguen y podamos acceder mediante el dominio, después de eso notaremos que si utilizamos la dirección con www nos redirige (de esto se encarga la configuración de Nginx),  por lo que todo quedo como esperabamos :).

Sin duda parece engorroso el proceso (pero es lo que hay  ~~por ser hipsters y utilizar una plataforma no mainstream :p~~), sin embargo es parecido al que haríamos en otras plataformas, aunque WordPress ofrece mas facilidades para elegir la preferencia del dominio por ejemplo.

Ahora que todo esta listo (por fin!) todos podemos comenzar a disfrutar tu blog o sitio web, si tienen dudas no olviden dejar un comentario.
