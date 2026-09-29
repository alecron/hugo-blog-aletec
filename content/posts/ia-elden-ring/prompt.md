# Prompt original

Guardado porque me gustó cómo lo escribí y quiero reusar este tono y esta
estructura en próximos posts. No se publica, es material de trabajo.

---

quiero hacer otro post/video comentando que me he hecho una herramienta par aque
la IA me ayude a jugar a videojuegos. Suelo tener muy poco tiempo para jugar y
eso hace que no recuerde lo que hago, y que en juegos como elden ring donde las
horas que necesitas para hacer algo relevante crecen exponencialmente es un
problema, sobre todo si como yo te has pegado 40 horas de partida y luego un año
entero sin jugar. No se ni que jefes habia derrotado, ni que habia hecho y si lo
intentaba por mi cuenta podia haberme pegado tranquilamente 2 horas jugando hasta
entender que estaba pasando. Asi que hice lo que cualquier desarrollador haría, y
le pedí a la IA que me creara varias herramientas para que analizara mi partida
guardada y me dijera en todo momento por donde iba, que tenia que hacer y
basicamente que misiones y objetos me quedaba por desbloquear. Por un lado me
hice como primera aproximacion un MCP (aqui explicaria que es un MCP muy por
encima, con metafora de una caja de herramientas) que permite a claude y a
cualquier agente leer mi Savegame del elden ring, que los souls en general usan un
sistema propietario llamado SL2 (que me gustaria explicar mas en detalle en el
vídeo, contando un poco la idea detras de este sistema de guardado), donde puedes
ver los IDs de los objetos y de los bosses y elementos principales y hacer a
partir de ese sistema un seguimiento. Lo que hizo mi claude aqui es gnerar un MCP
para poder consultar distintas partes de este guardado, como por ejemplo una
herramienta para ver el status de mi personaje y el nivel, otra para ver mi ultima
gracia de guardado y cosas asi. Esto me resulto demasiada sobreingenieria, yo
queria algo simple y mas visual, asi que le pedi, copiando un poco la idea de
elden ring tracker, que me generase un mapa interactivo con iconos donde pudiera
ver las cosas que he hecho y las que no, y que metiera ademas una pestaña de ruta
basandose en la guia de fextralife que me flipo, me parecio supercompleta y
normalmente usaria esta guia yo por mi cuenta y la iria leyendo, pero si le puedo
preguntar directamente al sistema que tengo que hacer, me ahorra ir leyendo....
no??? asi que aproveche y meti checkboxes basandose en esta guia y algunos de
ellos incluso se actualizan automaticamente basandose en si has conseguido el
objeto de la mision o no. Finalmente, estando ya ubicado y sabiendo lo que habia
hecho, simplemente me fui a gemini, que tiene todo el conocimiento de google, y le
pregunte "Estoy en la quest de Ranni, si tengo este objeto, que tengo que hacer
despues?" y gemini me contesto bastante rapido y con fuentes contrastables de
guias y al final fue la solucion que consiguio mas facilmente que siguiera
utilizandola. Mi conclusión de todo esto es que primero y sobre todo me lo pase
super bien jugando con la IA y añadiendo capas de aprendizaje y prueba y error al
proceso de jugar, luchando con una barrera mental que tengo siempre con los juegos
que es no droppearlos y ser capaz de retomar algo luchando contra la pereza que
dan las horas de recordar que habia hecho. En segundo lugar y como siempre...
muchas veces la solución más fácil y obvia es la mejor. Muchas empresas se matan a
hacer MCPs, aplicaciones vibecodeadas que cubren muchos casos de uso, bases de
datos superconectadas o miles de documentos en un vault de obsidian.... y al final
la mejor solución es la que habia que tomar simepre cuando desarrollabamos: buscar
en google, tener buenas APIs y mantener una buena documentación, ni demasiada ni
demasiado poca, la necesaria para entender el contexto. Los agentes de codigo son
como nosotros, les gusta fliparse, y como hobby para trastear esta guay, pero
siempre vamos a volver a la solución que nos cueste menos trabajo arrancar y
mantener.

---

# Notas para el vídeo largo

Los dos puntos que marqué para expandir, con material ya verificado, más beats que
en el post van comprimidos y en vídeo dan de sí.

## Expandir: qué es un MCP

En el post va en cuatro frases. En vídeo tiene recorrido.

- La metáfora de la caja de herramientas funciona, pero el remate es la parte que
  la hace valiosa: **la caja es intercambiable**. El mismo MCP lo usa Claude o
  cualquier otro agente. Ahí es donde se entiende por qué es un estándar y no una
  integración.
- Contraste útil para el vídeo: antes de MCP, cada integración era a medida para
  cada modelo. Es el paso de "cable propietario por aparato" a USB.
- Momento de pantalla: enseñar la lista de herramientas que expone tu MCP, con sus
  descripciones. Se ve muy claro que el agente elige según la etiqueta.
- Y un aparte honesto: la barrera de entrada de escribir un MCP es baja, y ese es
  justo el problema del que habla el post. Es tan fácil montarlo que lo montas sin
  preguntarte si hacía falta.

## Expandir: el formato SL2, que es lo mejor de la historia

Material verificado en la Souls Modding Wiki, con la referencia en el post:

- **No es un formato de guardado, es un contenedor.** `.sl2` por fuera, pero los
  primeros cuatro bytes son `BND4` (`0x34444E42`), el formato de empaquetado que
  FromSoftware reutiliza por todo su catálogo. Momento de pantalla clarísimo:
  abrirlo en un editor hex y señalar el magic.
- **Once entradas** `USER_DATA000` en adelante. Diez son personajes. La última
  guarda la información del menú principal, que es un detalle encantador.
- **Cada ranura ocupa exactamente `0x060030` bytes**, fijo. No crece con tu
  partida. Eso dice mucho de cómo se diseñaba pensando en consola y en memoria
  predecible.
- **AES con clave fija incrustada en el juego**, más un IV aleatorio por entrada
  que se guarda sin cifrar al principio de la propia entrada. Explicar por qué
  tiene que ser así: si el IV estuviese cifrado, no habría forma de empezar a
  descifrar. Ni para ti ni para el juego.
- **El mejor gancho del vídeo**: los 16 bytes de MD5 del principio de cada entrada
  no son protección anticopia. Son un chequeo de integridad para que el juego no
  te muestre "Save Data is Corrupted". Todo ese aparato criptográfico no está para
  pararte, está para que el juego confíe en su propio fichero. Si recalculas el
  MD5, se lo traga. Es una cerradura que protege de la humedad, no de los ladrones.
- Las claves de DS3 y de Dark Souls Remaster están públicamente documentadas en la
  wiki. En el post decidí no pegar el hex porque el dato interesante es que la
  clave es fija y pública, no el hex en sí. Para el vídeo, misma decisión.
- Debajo de todo eso está lo único que yo quería: los **IDs** de objetos, jefes y
  gracias. La partida es, en el fondo, una base de datos.

## Beats que en el post quedan comprimidos

- **El momento "tengo un microscopio y necesitaba un mapa"** merece su escena. Es
  el giro de la historia.
- **La autojustificación del "¿no? ¿no es eso lo razonable?"** cuando decido no
  leer la guía de Fextralife y preguntarle a un sistema que tengo que construir yo
  primero. Ese autoengaño es el corazón cómico del vídeo y en texto se pierde.
- **Los checkboxes que se marcan solos** cruzando ID del save con la guía. Es lo
  técnicamente más bonito que hice y en pantalla se ve genial.
- **El anticlímax de Gemini** hay que dejarlo respirar. Silencio, se abre el
  navegador, se escribe la pregunta, responde. Sin música épica.

## Cosas que quiero verificar antes de grabar

- Si mi MCP sigue funcionando tras los últimos parches. Hay un issue conocido de
  saves de Elden Ring 1.17.1 fallando con "Incompatible Save File Format" en
  parsers de la comunidad, así que el formato se mueve entre versiones. Buen
  detalle para mencionar: mantener esto es trabajo, que es justo la tesis.
- Elden Ring Tracker rastrea cerca de tres mil objetos. Conviene confirmarlo antes
  de decirlo en cámara.
- Si quiero enseñar código en pantalla, decidir cuánto del MCP muestro.

## Posible título alternativo

"Monté un lector de ficheros binarios cifrados para no tener que leerme una guía"
