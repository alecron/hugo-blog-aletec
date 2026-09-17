+++
date = '2024-10-20T01:39:32+02:00'
draft = false
title = '¿Cómo funciona esto?'
backgroundImage = 'img/background.svg'
+++

O sea, tampoco es un drama ni nada, pero... ¿cómo puede ser tan difícil hacer que funcione una imagen de fondo?

Voy a soltar aquí un poco de Lorem Ipsum, amigo, solo para ir probando:

Lorem ipsum dolor sit amet, consectetur adipiscing elit sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

## La imagen

![La imagen](img/background.svg)

Ahí está, la imagen de la que hablaba. Es un simple archivo SVG que quería usar como imagen de fondo. Pero no funciona. No sé muy bien por qué, aunque lo acabaré averiguando. Estoy seguro de que sí.

## La solución

Bueno... quizá me quede este post de prueba por aquí, por si a alguien más le pasa lo mismo.

La cosa era que estaba poniendo el parámetro `defaultBackgroundImage` dentro de la sección de la homepage en el archivo `config.toml`, cuando en realidad había que ponerlo en la raíz del archivo.

Puede que me pase a yaml o json para la configuración, simplemente para que sea más fácil de leer y de entender, porque el formato toml no me acaba de gustar.

### Casi me olvido

No me gustaba el layout de fondo por defecto, me parecía más atractivo el layout `Profile`. El problema es que la imagen de fondo no funciona con ese layout, así que me hice uno propio tomando el de profile como base y añadiéndole la imagen de fondo. Si te apetece echarle un ojo, está en la carpeta `layouts/partials/home`.

![alt text](image.png)
