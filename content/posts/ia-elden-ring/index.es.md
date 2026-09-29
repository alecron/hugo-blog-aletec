+++
date = '2026-09-29T09:00:00+02:00'
draft = true
title = 'Le pedí a la IA que me recordase mi partida de Elden Ring y acabé volviendo a Google'
slug = 'ia-para-jugar-elden-ring'
description = 'Monté un MCP para leer mi savegame de Elden Ring, un mapa interactivo con la ruta de Fextralife y checkboxes automáticos. Y al final lo que funcionó fue preguntarle a Gemini. Una historia sobre sobreingeniería.'
tags = ['IA', 'MCP', 'videojuegos', 'Elden Ring', 'desarrollo']
+++

En Elden Ring el jefe más difícil no es Malenia. Es abrir la partida después de un año y no tener ni idea de quién eres.

Eso me pasó. 40 horas de partida, un parón de un año entero, y al volver: un tío con una espada, en un sitio, con un inventario lleno de cosas que no recordaba haber cogido. ¿Qué jefes había matado? Ni idea. ¿En qué misión andaba? Menos. ¿Por qué tenía tres campanas distintas? Un misterio.

Y aquí está el problema de verdad, el que no se habla: en un juego como este, **las horas que necesitas para hacer algo relevante crecen de forma exponencial**. Al principio avanzas cada quince minutos. A las 40 horas, cualquier cosa que importe son dos o tres sesiones. Y si además tienes poco tiempo para jugar, la ecuación se rompe: pasas más rato recordando qué hacías que jugando.

Calculé que reconstruir mi propia partida a mano, a base de dar vueltas y abrir menús, me iba a costar tranquilamente **dos horas**. Dos horas de mi tiempo de ocio dedicadas a hacer arqueología de mí mismo.

Así que hice lo que haría cualquier desarrollador, que es no jugar y ponerse a programar.

## Capa 1: un MCP para leer mi partida

### Qué es un MCP, rápido

Imagina que le das a la IA una caja de herramientas.

El modelo, por sí solo, sabe hablar. Sabe razonar. Pero no puede tocar nada de tu ordenador. Un MCP es el estándar que define cómo se le pasa esa caja: dentro metes herramientas concretas, cada una con su etiqueta de para qué sirve, y el agente decide cuál coger según lo que le pidas.

Tú no le explicas cómo usar el destornillador. Le dices "aprieta ese tornillo" y él busca en la caja. Lo bonito es que la caja es intercambiable: el mismo MCP lo puede usar Claude, o cualquier otro agente, sin cambiar nada.

Así que pensé: si le doy una herramienta para leer mi partida guardada, ya está. Le pregunto y me cuenta.

### El pequeño detalle de que los Souls no te lo ponen fácil

Los ficheros de guardado de FromSoftware son `.sl2`. En mi caso, `ER0000.sl2`.

Y esto es lo que me enganchó, porque el formato es una pequeña joya de ingeniería antigua. Por dentro no es un `.sl2` cualquiera: **es un contenedor BND4**, el formato de empaquetado que FromSoftware usa en medio juego suyo. Si abres el fichero en un editor hexadecimal, los cuatro primeros bytes te lo cantan: `BND4`.

Dentro hay once entradas, llamadas `USER_DATA000` y sucesivas. Diez son tus ranuras de personaje. **La última no es un personaje: guarda la información del menú principal.** Cada ranura ocupa exactamente `0x060030` bytes, siempre lo mismo, ocupes lo que ocupes.

Y cada entrada va cifrada con AES, con una clave fija que está incrustada en el juego, más un vector de inicialización aleatorio que se guarda **sin cifrar** al principio de la propia entrada, porque si no nadie podría descifrarla, ni el juego.

Pero mi parte favorita es el checksum. Cada entrada empieza con 16 bytes de MD5. Y no está ahí para impedirte tocar nada: **está ahí para que el juego sepa que el fichero no se ha corrompido** y no te suelte la pantalla de "Save Data is Corrupted". No es protección anticopia, es un cinturón de seguridad.

O sea que todo ese aparato criptográfico no existe para pararte los pies. Existe para que el juego pueda confiar en su propio fichero. Y eso significa que si recalculas el MD5 después de tocar algo, el juego se lo traga encantado. La comunidad de modding lleva años documentándolo, con herramientas para desempaquetar saves de DS3, analizadores de `.sl2` y hasta vigilantes de savegame en tiempo real.

Debajo de esa capa está lo que yo quería: **IDs**. Los objetos tienen ID, los jefes tienen ID, las gracias tienen ID. Y con eso puedes reconstruir una partida entera como si fuera una base de datos.

### Lo que salió

Le pedí a Claude que me montase el MCP, y lo hizo bien. Herramienta para ver el estado y el nivel de mi personaje. Herramienta para consultar la última gracia en la que había descansado. Herramientas para asomarse a distintas partes del guardado.

Funcionaba. Y era **sobreingeniería pura**.

Porque yo no quería una API sobre mi partida. Yo quería saber qué narices tenía que hacer esta noche. Tenía un microscopio y lo que necesitaba era un mapa.

## Capa 2: el mapa, que ya se parecía más a algo

Así que cambié de enfoque, robándole la idea sin disimulo a **Elden Ring Tracker**, que lee tu save y te pinta el progreso en un mapa interactivo.

Le pedí un mapa con iconos: esto hecho, esto no. Visual, de un vistazo, sin leer.

Y luego le añadí la pieza de la que estoy más orgulloso: **una pestaña de ruta basada en la guía de Fextralife.**

Aquí quiero defender a Fextralife un segundo, porque me flipa. Es supercompleta, está bien ordenada y normalmente yo la usaría tal cual, leyéndola a trozos mientras juego. Pero si puedo preguntarle directamente al sistema qué toca ahora, me ahorro el ir leyendo. ¿No? ¿No es eso lo razonable?

Así que metí checkboxes siguiendo esa guía. Y algunos, los que puedo cruzar con un ID del savegame, **se marcan solos**: si ya tengo el objeto de esa misión, la casilla aparece hecha sin que yo toque nada.

Eso ya era útil de verdad. Abría el mapa y en diez segundos sabía dónde estaba.

Pero seguía habiendo un problema, y era que lo había construido yo. Cada vez que quería una cosa nueva, tocaba programar.

## Capa 3: el anticlímax

Ya ubicado, ya sabiendo lo que había hecho, me quedaba la pregunta concreta. Y en lugar de seguir extendiendo mi propio invento, abrí Gemini y escribí algo así:

> Estoy en la quest de Ranni, si tengo este objeto, ¿qué tengo que hacer después?

Y me contestó. Rápido. Con pasos claros. Y con fuentes de guías que podía abrir y contrastar, que es la parte que importa.

Eso fue todo. Ese fue el momento en el que el proyecto se acabó, porque **esa fue la solución que conseguí seguir usando**.

No la más elegante. No la que más me lucía. La que no me costaba nada arrancar.

## Lo que me llevo

### Uno: me lo pasé genial, y eso no es un detalle menor

Jugar con la IA metida en el proceso fue divertidísimo. Añadí una capa de aprendizaje y de prueba y error encima de un juego que ya me gustaba, y eso lo hizo más mío.

Pero hay algo más importante. Yo tengo una barrera mental con los videojuegos: **no soy capaz de retomarlos.** No los dejo porque me aburran, los dejo porque volver da una pereza monumental. La pereza no es de jugar, es de *recordar*.

Todo este tinglado, MCP incluido, era en realidad una máquina para derrotar esa pereza. Y funcionó. Volví a jugar. Eso solo ya justifica el fin de semana.

### Dos: la solución obvia gana, otra vez

Y esta es la parte que quiero que se quede.

Muchas empresas se están matando ahora mismo en esto: MCPs para todo, aplicaciones vibecodeadas que cubren cincuenta casos de uso, bases de datos superconectadas, miles de documentos en un vault de Obsidian. Arquitecturas preciosas.

Y al final la mejor solución es la que siempre había que tomar cuando desarrollábamos: **buscar en Google, tener buenas APIs y mantener buena documentación.** Ni demasiada ni demasiado poca. La justa para entender el contexto.

Yo monté un lector de un formato binario cifrado de FromSoftware para saber en qué misión andaba. Y me lo resolvió una búsqueda con fuentes.

### Tres: a los agentes les gusta fliparse, igual que a nosotros

Cuando le pides a un agente de código que te resuelva algo, te construye lo que tú construirías en tu mejor día: bonito, extensible, con capas. Porque ha aprendido de nosotros, y nosotros nos flipamos.

No se lo reprocho, yo hago lo mismo. Pero conviene saberlo, porque si no le pones tú el freno, no lo va a poner él.

Como hobby para trastear, montar cosas así está genial y lo volvería a hacer mañana. Pero para resolver un problema de verdad, **siempre vamos a volver a la solución que cueste menos arrancar y menos mantener.** Y eso casi nunca es lo que acabamos de construir.

Ahora, si me disculpáis, tengo que ir a que me mate Malenia. Que para eso sí que no hay MCP.

## Fuentes

- [SL2 Save Files](http://soulsmodding.wikidot.com/format:sl2), Souls Modding Wiki
- [Souls Modding: SL2 Files](https://sites.google.com/view/soulsmods/file-formats/sl2-files)
- [sl2-analyzer](https://github.com/darthdemono/sl2-analyzer), lector de `.sl2` para siete juegos de FromSoftware
- [DS3SaveUnpacker](https://github.com/tremwil/DS3SaveUnpacker/), empaquetado y desempaquetado de saves de Dark Souls III
- [er-save-watcher](https://github.com/tvhdev/er-save-watcher)
- [Elden Ring Tracker](https://steamcommunity.com/sharedfiles/filedetails/?id=3698397193), mapa interactivo que lee tu partida
- [Elden Ring Wiki](https://eldenring.wiki.fextralife.com/Interactive_Map), Fextralife
- [Model Context Protocol](https://modelcontextprotocol.io/)
