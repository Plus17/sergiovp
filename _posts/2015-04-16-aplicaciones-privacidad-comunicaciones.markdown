---
title: '5 aplicaciones para mantener la privacidad en las comunicaciones'
date: 2015-04-16 00:00:00 
tags: seguridad
layout: post
---
La **privacidad** es un tema delicado que se debe tratar con pinzas, sin embargo mas allá de la opinión de cada uno considero que esta es necesaria, de manera personal o corporativa por ejemplo. Aunque hay momentos en el que el tema explota y todos hablan de el, luego va bajando y salen algun texto por aquí y por allá

Aún así es algo de lo que considero no debería dejar de hablarse, siempre sera un tema que para bien o para mal se retomara, ya sea porque se han hecho avances en esta materia o porque hubo un nuevo intento de algún país para tener acceso libre e ilimitado a toda nuestra información personal.

Por cierto, los animo a leer el siguiente articulo, simplemente excelente: [Por qué la privacidad es necesaria: desmontando el "no tengo nada que ocultar"](http://www.xataka.com/otros/por-que-la-privacidad-es-necesaria-desmontando-el-no-tengo-nada-que-ocultar).

Así que aquí muestro algunas recomendaciones para ayudar a **mejorar la privacidad** (que no anonimato) o **seguridad** de nuestras **comunicaciones** por distintas vías:

## 1. Mensajería privada

En la mensajería móvil tenemos un líder indiscutible que es *WhatsApp* y su rival *Telegram*, el segundo es el que más se dice respetuoso de nuestra privacidad y preocupados por la seguridad, pero desde el inicio surgieron varias dudas al respecto, principalmente por el hecho de utilizar un protocolo propio (basado en otro) en lugar de uno abierto y probado por su seguridad, por eso la recomendación es:

* [TextSecure](https://whispersystems.org/#privacy) (Android y Signal para IOS)

En un principio esta app proporcionaba incluso cifrado para SMS, pero le han dicho [adiós](https://whispersystems.org/blog/goodbye-encrypted-sms/), aún así nos proporciona cifrado en los mensajes enviados mediante datos móviles o WiFi, por lo que nuestra privacidad se vera mejorada, además es de código.

Otro aspecto es que whatsapp trabajo junto a whispersystems (desarrolladores de Textsecure entre otras apps) para proveer mejoras a la seguridad de su propia aplicación, al menos para las conversaciones 1 a 1.

* [Pidgin + OTR](https://pidgin.im) (Windows, Linux)

A pidgin, cuantos no te conocen :), aunque este tipo de comunicaciones tipo messenger hace mucho que se "extinguieron", siempre podemos utilizar software como pidgin que es multiprotocolo y utilizar nuestras cuentas de correo, XMPP o incluso facebook para comunicarnos con nuestros contactos y si a esto le agregamos el plugin **Off-the-Record Messaging (OTR)** tendremos nuestros mensajes cifrados.

## 2. Llamadas

Aunque para **cifrar las llamadas** comunes que realizamos utilizando nuestra operadora existe hardware especializado, este es caro y poco accesible, para este tema nuestra mejor opción es hacer uso de datos móviles o una conexión WiFi y utilizar alguna aplicación estilo **Line o Viber** pero con énfasis en la seguridad:

* RedPhone (Linux, Singal en IOS)

De la misma gente que *TextSecure*, nos proporciona llamadas cifradas, ambas aplicaciones utilizan nuestro **numero telefónico**, además si llamamos a un contacto de forma normal y este tiene **RedPhone** instalado nos dará la opción de realizar la llamada utilizándolo. para mas información recomiendo el siguiente articulo (muy completo) [Cómo llamar por teléfono conservando la privacidad con Redphone ](http://www.hackplayers.com/2013/06/como-llamar-por-telefono-conservando-la-privacidad.html)

### Alternativas a Skype
Skype es una herramienta muy útil, aunque no muy recomendada por aquellos que se preocupan de la privacidad, aquí no hay muchas opciones pero en lo personal he probado **jitsi**:

* [Jitsi](https://jitsi.org/) (Windows, Linux, Mac)

Es un cliente multiprotocolo opensource (al igual que las anteriores), nos permite mantener chat y llamadas de voz o vídeo cifradas, bastante bueno en general. Lo mas próximo además de esta alternativa quizá sea [Tox](http://blog.desdelinux.net/tox-la-alternativa-open-source-skype/), aunque aún esta en desarrollo.

## 3. Correo electrónico
El **email** continua siendo un imprescindible en las comunicaciones, muchas veces se envía información sensible como datos bancarios, documentación personal entre otros sin ningún tipo de protección, pero podemos fácilmente mejorar esto.

* PGP

Aquí nuestra mejor alternativa es utilizar **GnuPG**, una implementación libre de **GPG** que nos permite cifrar de forma asimétrica cualquier mensaje que sera enviado mediante un medio potencialmente inseguro como el correo electrónico. 

Al decir asimétrico se refiere a que se utiliza una **clave publica** para cifrar, esta se le proporciona a quien quiera enviarnos un mensaje, y para descifrar la información se utiliza una **clave privada**, la cual no necesitamos compartir. Esto elimina el problema de tener que compartir la contraseña para descifrar un archivo por un medio tan o más inseguro como por el que pasaríamos la información :p.

Suena complejo y de cierta forma lo es, pero la gente de **FayerWayer** no explican como usarlo sin dolor en: [Qué es y cómo usar PGP en tu vida diaria](https://www.fayerwayer.com/2015/03/que-es-y-como-usar-pgp-en-tu-vida-diaria/).

###Bonus: utilizar VPN
Si queremos mejorar aún mas el tema de la privacidad y seguridad de nuestros datos siempre podemos optar por utilizar una **VPN**, estas generan un "tunel" por donde nuestra información viaja cifrada de nuestro equipo al destino.

Este tipo de soluciones es especialmente recomendable si utilizamos redes inalámbricas abiertas, en el aeropuerto o ese evento geek donde todos están conectados a la misma red, ya que nunca falta el que usa un sniffer para escuchar las comunicaciones que pasan, en estos casos también son tremendamente útiles para mejorar nuestra seguridad.
