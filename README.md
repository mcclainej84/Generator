# README — CodexMaker

Generador de fichas de unidad y personaje (Warhammer). Este archivo recoge el historial completo de cambios, versión a versión. A partir de ahora se mantiene aparte del `.html` para poder adjuntarlo o consultarlo sin abrir el programa.

Para un versionado real (poder volver atrás con garantías), usa Git: haz un commit cada vez que subas una versión nueva a tu repositorio, y opcionalmente crea un tag (`v1.0`, `v1.1`...) para marcar los hitos importantes.

---

## v4.7

- **Arreglado el agrupado de fichas por página en "Word texto"**: la exportación mide el alto real de cada ficha para decidir dónde hace falta forzar un salto de página y dónde no (agrupando varias en la misma hoja cuando caben), pero medía con `scrollHeight`, que ignora el recorte de "Alto máximo de la ficha" (`max-height` + `overflow:hidden`) y siempre devuelve el alto del contenido completo sin recortar — más alto de lo que en realidad se ve. Resultado: fichas que visualmente cabían de sobra juntas en una página casi nunca se agrupaban. Cambiado a `offsetHeight`, que sí respeta ese recorte. Probado con varias fichas a 420-440&nbsp;px: ahora se agrupan de dos en dos como se espera, y una ficha grande de por medio sigue forzando su propio salto de página. "Word con imágenes" no tenía este problema (mide el tamaño real de la captura, no el del DOM).
- **Texto del aviso "Ideal para 2 fichas por página"**: corregido para que diga eso en vez de "Ideal para 2 páginas", que no describía bien lo que indica el aviso.

---

## v4.6

- **Aviso "Ideal para 2 páginas"**: junto al control de "Alto máx." de la barra superior aparece un pequeño aviso en verde cuando el alto máximo de la ficha activa es de 480&nbsp;px o menos, como referencia de que a esa altura suelen caber dos fichas cómodamente en la misma página al exportar. Es solo un indicador en la interfaz (vive en la barra superior, fuera de la ficha): no aparece nunca en las exportaciones ni al imprimir.
- **Autocompletar perfil por nombre**: al terminar de escribir el nombre de un perfil en "Perfiles de características" (al salir del campo, o con Intro) si ya existe un perfil con ese mismo nombre —en esta ficha o en cualquier otra— se copian automáticamente sus 9 valores, con un pequeño destello amarillo en las celdas rellenadas para que se note el cambio. Deliberadamente se comprueba solo al terminar de escribir (blur/Intro), no en cada pulsación de tecla: con el volumen de datos real de esta herramienta (unas pocas decenas de fichas) no hace falta mantener un índice cacheado aparte, y evitarlo evita también el riesgo de que ese índice se desincronice si se olvida invalidarlo en algún punto.

---

## v4.5

- **"Libro existente" — insertar en una página concreta, no solo al final**: nuevo campo "Insertar las fichas a partir de la página". En blanco (por defecto) se sigue añadiendo todo al final, como en la v4.3/v4.4. Si se indica un número, las fichas nuevas se insertan justo ahí, y lo que hubiera de esa página en adelante se desplaza después de las fichas nuevas, sin borrarse — igual en `.doc`/`.htm`/`.html` que en `.docx` (contando "página" por los saltos de página manuales del documento, como ya se explicaba). Probado insertando en medio, al principio, y con un número de página mayor que las páginas que tiene el documento (en ese caso se comporta como "al final", sin romper nada).

---

## v4.4

- **"Libro existente" — soporte real de `.docx`**: en vez de "convertir" el `.docx` a otra cosa (que es lo que lo destrozaba en intentos anteriores), ahora se edita directamente por dentro. Un `.docx` es en realidad un `.zip` con archivos XML dentro (formato OOXML); esta versión lo abre con JSZip, añade los párrafos y tablas de las fichas nuevas como XML de Word de verdad justo antes de que se cierre el documento (respetando que la información de página, si existe, debe seguir siendo lo último), y vuelve a comprimirlo. Nada del resto del archivo se toca: estilos, imágenes, tablas o texto que ya hubiera se quedan exactamente igual. Para "Word con imágenes", cada ficha se incrusta como una imagen real dentro del propio `.docx` (con su relación y su entrada en `word/media/`, como haría el propio Word al pegar una imagen).
- **Verificado con herramientas independientes, no solo "a ojo"**: se generó un `.docx` de prueba con `python-docx` (con título, párrafos, una tabla y texto con color), se le añadieron fichas con esta herramienta, y el resultado se volvió a abrir con `python-docx` — una librería totalmente independiente de este proyecto — confirmando que el documento resultante es válido, que el contenido original no se ha movido ni alterado, y que las imágenes incrustadas se reconocen como imágenes de Word normales y corrientes, con las dimensiones correctas.
- **Aviso realista sobre el aspecto de la ficha en modo `.docx`**: por este camino la ficha se genera como párrafos y tablas de Word normales (no HTML, así que no puede reutilizar el maquetado visual de la vista previa); es un poco más sencilla que en el resto de exportaciones — por ejemplo, la ilustración de posición libre no está disponible con "Word texto" en este modo — pero con el mismo contenido.
- Los `.doc`/`.htm`/`.html` (incluido lo que exporta esta misma herramienta) siguen funcionando exactamente como en la v4.3: conservando el documento original completo y añadiendo el contenido nuevo al final.

---

## v4.3

- **"Libro existente" de vuelta, pero mucho más simple**: en vez de intentar insertar el contenido nuevo en medio del documento (a partir de "la página X"), ahora solo se añade **al final** del documento adjuntado. Nada del documento original se reordena ni se mueve: los nodos originales se quedan exactamente donde estaban, y el contenido nuevo se añade como último elemento. Es una operación mucho más simple (sin cálculo de "páginas", sin dividir ni reordenar nada) y por tanto con muchas menos cosas que puedan salir mal. Sigue sin admitir `.docx` (ver v4.1/v4.2): solo `.doc`/`.htm`/`.html`, que es donde se puede hacer sin perder el formato original. Probado con un documento con estilos propios en el `<head>`: el resultado los conserva intactos, el contenido original no cambia de posición, y exportar varias veces seguidas con el mismo documento adjuntado no acumula copias.

---

## v4.2

- **"Libro existente" retirado — no era fiable**: a pesar del arreglo de la v4.1 (conservar el `<head>` original), sigue sin funcionar de forma fiable con documentos Word reales: llevan demasiadas cosas por dentro (secciones, imágenes incrustadas, numeración de listas, referencias cruzadas...) como para que un script en el navegador los recomponga bien sin el propio Word. En vez de insistir con una solución a medias, se ha retirado esa función. "Word texto" y "Word imág." vuelven a generar siempre un documento nuevo e independiente, como en la v3.5 y anteriores.
- **En su lugar, una guía para hacerlo con el propio Word**: nueva sección "¿Añadir esto a un libro que ya tienes?" en "Tus fichas", con los pasos para usar la función nativa de Word **Insertar → Objeto → Texto de un archivo** (Insertar → Texto → Insertar texto de archivo, en Word Online): se exporta el documento de fichas desde aquí, y se inserta con Word en el punto exacto del libro existente que se quiera, con Word encargándose de mover el contenido posterior y de mantener el formato — que es exactamente lo que se buscaba, pero hecho con una herramienta capaz de hacerlo bien.

---

## v4.1

- **Libro existente — arreglado el destrozo de formato**: la primera versión de esta función (v4.0) extraía solo el contenido del `<body>` del documento adjuntado y lo volvía a montar con la plantilla genérica de esta herramienta, tirando por el camino el `<head>` original — que es justo donde un documento Word-como-HTML guarda casi todo su aspecto real (clases `mso-style-*`, tipografías, colores...). Resultado: el documento adjuntado perdía su formato al exportar. Corregido de raíz: para `.doc`/`.htm`/`.html` (incluidos los que exporta esta misma herramienta, o los que genera Word con "Guardar como > Página web") ya **no se reconstruye nada**; se conserva el documento original completo (head, estilos, espacios de nombres, todo) y solo se insertan o desplazan fragmentos de contenido dentro de él, usando los nodos originales tal cual. Verificado insertando en documentos con estilos propios en el `<head>`: el resultado conserva ese `<head>` exactamente igual, sin duplicar ni perder nada, y exportar varias veces seguidas con el mismo libro adjuntado da siempre el mismo resultado (se vuelve a leer el archivo original desde cero en cada exportación, nunca se acumulan cambios de una exportación a la siguiente).
- **`.docx` moderno — limitación real, no un bug**: para poder leerlo en el navegador, esta herramienta usa Mammoth.js, que por diseño solo extrae texto y formato básico (negrita, cursiva, títulos) y descarta el resto (fuentes, colores, saltos de página) — eso ocurre en el momento de leer el archivo, antes incluso de insertar nada, así que cambiar dónde se inserta el contenido no lo arregla. El aviso en la propia app ahora lo explica así, y recomienda guardar el documento desde Word como página web (`.htm`) y adjuntar ese archivo en su lugar si se quiere conservar el formato.

---

## v4.0

Rediseño de la interfaz (probado antes como prototipo aparte, ahora ya es la versión oficial):

- **Barra superior fina**: arriba de todo solo queda lo que afecta a todo el archivo — Guardar/cargar, Vista (color/blanco y negro, marco, alto máximo de la ficha) y Escudo del ejército — con todos los botones normalizados a la misma altura, tipografía y estilo (clase `.tb-btn` reutilizable, pensada para poder añadir más botones ahí en el futuro sin descuadrar nada).
- **Panel izquierdo**: solo los campos específicos de la ficha activa (Cabecera, Montura, Ilustración, Perfiles, Equipo, Opciones, Grupo de mando, Reglas especiales).
- **Ficha centrada**: la ficha se ve centrada (horizontal y verticalmente) sobre un fondo de textura de pergamino, en vez de pegada a un lado.
- **Panel derecho — "Tus fichas"**: la lista de fichas, los botones para crear/exportar, y la nueva sección "Libro existente" (ver más abajo). Se puede redimensionar igual que el panel izquierdo, y ahora **el ancho de ambos paneles se recuerda entre sesiones** (vía `localStorage` del navegador).
- **Marcar ficha como completada**: nuevo control discreto debajo de la ficha (checkbox nativo con `accent-color`, sin iconos ni cajas de color llamativas) para marcar internamente que una ficha ya está terminada. Es solo una marca para ti: nunca aparece en la vista impresa ni en ninguna exportación (PNG, Word, Hoja de referencia), porque vive fuera del recuadro de la ficha. Las fichas completadas se distinguen en "Tus fichas" con un borde verde (más intenso tras el primer ajuste); un icono de ojo/ojo tachado (SVG, no emoji) permite ocultarlas de la lista sin perder los datos.
- **Consistencia de campos e inputs, revisada a fondo**: se encontró que varios campos (nombre de perfil y sus valores en Perfiles de características, Opciones, nombre de Montura) no tenían el atributo `type="text"`, así que el CSS de la app no los alcanzaba y se veían con el estilo por defecto del navegador (borde gris hundido, sin redondeado, tipografía distinta). Corregido: ahora todos los campos de una línea miden 28px de alto con el mismo borde y tipografía. De paso, todos los campos de puntos (puntos por miniatura, músico, portaestandarte, campeón, coste de montura) dejan de estirarse al 100% del contenedor y se limitan a 70px de ancho (de sobra para 999).
- **Botones "elegir archivo" unificados**: los cuatro que abren un selector de archivos (Cargar .json, escudo del ejército, escudo de la ficha, elegir imagen de la ilustración) comparten ahora la misma clase (`.tb-btn`), así que se ven exactamente igual estén donde estén, en vez de depender de si el elemento real era un `<button>` o una `<label>`.
- **Ilustración**: el selector de imagen ya no depende del "Choose File / No file chosen" nativo (sin traducción posible); ahora es un botón "📂 Elegir imagen" con "🗑️ Quitar imagen" justo al lado, todo en español.
- **"Tus fichas" — botones reorganizados**: Nueva unidad/Nuevo personaje arriba, lista en medio, y abajo "Marcar todas", "Desmarcar todas", "Vaciar ficha" y "Borrar todas" en una sola fila con el mismo estilo entre sí (solo los dos destructivos van en rojo). Más espacio antes de la casilla "Incluir la Hoja de referencia al final", para que no se vea todo apretado.
- **Libro existente**: nueva sección en "Tus fichas" para adjuntar un documento ya existente (`.doc`/`.docx`/`.htm`/`.html`) e indicar a partir de qué página insertar las fichas. Al exportar a Word (texto o imágenes) con un libro adjuntado, en vez de crear un documento nuevo se inserta todo el contenido nuevo dentro de ese documento, a partir de la página indicada; si esa página (y las siguientes) ya tenían contenido, se conserva íntegro pero se desplaza al final, después de las fichas nuevas — nunca se borra nada. Los `.docx` modernos se leen con [Mammoth.js](https://github.com/mwilliamson/mammoth.js) (cargado desde cdnjs), con conversión aproximada; los `.doc`/`.htm`/`.html` (incluidos los que exporta esta misma herramienta) se leen de forma nativa y con mejor fidelidad. **Aviso importante y limitación real, no cosmética**: como estos documentos son en el fondo HTML disfrazado de Word, no existe una forma de calcular la paginación "visual" real de Word sin el propio Word (el número de página no se guarda como tal en el archivo). Por eso "página" aquí se define como *el tramo de contenido entre dos saltos de página manuales* — los mismos con los que esta herramienta separa unas fichas de otras. Si el documento adjuntado no tiene saltos de página manuales (lo habitual si viene de un .docx moderno convertido con Mammoth, que no los conserva), se trata como un documento de una sola página.

---

## v3.5

- **Word con imágenes — línea en blanco encima de cada ficha, y menos saltos de página innecesarios**: cada imagen sigue llevando un párrafo en blanco justo encima (y ahora también uno al final del documento) para poder escribir algo a mano; además, ya no se envuelven todas las imágenes juntas dentro de un mismo `<div>`, que era lo que hacía que, si una imagen no cabía entera en el hueco que quedaba de página, Word arrastrara consigo también el resto de imágenes a la página siguiente aunque sí que cupieran. Ahora cada imagen es un párrafo suelto y Word decide su sitio de forma independiente.
- **Word con texto — ya no se fuerza una ficha por página**: se ha quitado el salto de página obligatorio entre fichas. Si dos fichas son pequeñas, ahora pueden compartir la misma hoja en vez de arrancar página nueva sin necesidad (si no caben juntas, Word las separa solo, como es de esperar).
- **Word con texto — tipografía y márgenes ligeramente más compactos**: para ayudar a que quepan 2 fichas por página sin que se vea recargado, se ha reducido *muy poco* el tamaño de letra de la ficha (título, barra, tabla de atributos, equipo/opciones/reglas) y se han recortado también un poco los márgenes internos y el espacio entre fichas consecutivas. Esto solo afecta al documento Word exportado, no a la vista previa en pantalla ni al PNG.

---

## v3.4

- **Hoja de referencia — ancho completo y centrada**: las tablas ahora ocupan todo el ancho útil de la página (antes se quedaban más estrechas, pegadas a la izquierda). Las columnas de atributos siguen midiendo todas exactamente lo mismo entre sí; el espacio extra que gana la tabla al estirarse va a la columna de "Reglas especiales" (la que más texto suele llevar). Títulos centrados también. De paso, se ha reducido un poco (muy poco) el tamaño de letra del contenido de las tablas, para compensar el ancho ganado sin que se vea desproporcionado.

---

## v3.3

- **Monturas — mismo look and feel que Opciones**: las filas de la lista de "Montura" (en fichas de personaje) ahora tienen exactamente el mismo aspecto, espaciado y tamaño de letra que las filas de "Opciones" (llevan el icono ⠿ delante). Además, ahora se pueden reordenar arrastrando por ese icono, igual que ya se podía con las opciones.

---

## v3.2

- **Cargar `.json` — arreglo de selección en el primer clic**: el input de archivo oculto que hay detrás del botón "📂 Cargar (.json)" usaba `display:none`, lo que en algunos navegadores (sobre todo Safari) podía impedir que se abriera el selector de archivos la primera vez que se pulsaba. Cambiado al patrón "visualmente oculto" (en vez de `display:none`), que es el recomendado para este caso y funciona de forma fiable en todos los navegadores.
- **Escudo del ejército — botón para quitarlo**: nuevo botón "🗑️ Quitar escudo del ejército" junto al selector de logo/escudo por defecto, para poder deshacer la subida y dejar el hueco en blanco de nuevo (como al principio), sin tener que recargar la página. El botón para quitar el escudo específico de una ficha concreta ya existía desde antes ("Usar el del ejército en esta ficha").
- **Hoja de referencia al final del Word**: nueva casilla "Incluir la Hoja de referencia al final" en la sección de exportación, disponible tanto para "Word texto" como para "Word imág.". Si se marca, añade al final del documento (en una página aparte) la misma Hoja de referencia que genera el botón dedicado, pero limitada a las fichas marcadas para exportar (no a todas las del archivo).
- **Sección "IA" renombrada a "IA (Experimental)"**: para dejar más claro desde el propio título que ni el generador de bocetos ni el "blanquear fondo" son herramientas terminadas o fiables al 100%.
- **Grupo de mando oculto en personajes**: la sección "Grupo de mando" del panel (Músico, Portaestandarte, Campeón) ahora solo se muestra para fichas de tipo unidad. Los personajes nunca han tenido Grupo de mando en el juego, así que no tiene sentido que apareciera (ni en el panel ni, si por lo que fuera tenía datos heredados, en la ficha o en las exportaciones).
- **`CHANGELOG.md` renombrado a `README.md`**: mismo contenido (el historial de versiones), simplemente con el nombre de archivo que GitHub muestra automáticamente en la página principal de un repositorio.

---

## v3.1

- **"Resumen de perfiles de unidades" renombrado a "Hoja de referencia"**: además del cambio de nombre (botón y archivo `.doc` descargado), se ha rehecho el formato para parecerse al de la Hoja de referencia real de los Manuscritos de Nuth. Antes generaba dos páginas separadas (una tabla con todos los atributos, y otra aparte con las reglas especiales de cada ficha). Ahora es una única tabla por grupo (**Personajes** y **Unidades**, según el tipo de cada ficha) con la columna "Reglas especiales" integrada a la derecha de los atributos, en vez de en una tabla aparte, con filas compactas (poco margen entre líneas) para que quepa el máximo de contenido posible. **Aviso**: no incluye columna TSA (CodexMaker no guarda ese dato por perfil) y, como las reglas especiales se guardan a nivel de ficha y no de perfil individual, la columna de reglas se rellena una sola vez por ficha (abarcando con `rowspan` también los perfiles de montura, si los tiene), no una por perfil como en el original.

---

## v3.0

- **Ilustración — nombre de archivo**: al subir una imagen, se recuerda el nombre del archivo original y se muestra en pequeño bajo el selector de archivo (y como tooltip al pasar el cursor por encima de la imagen en la ficha). Las fichas cargadas de un `.json` anterior a este cambio no lo tendrán, y no pasa nada.
- **Ilustración — voltear horizontalmente**: nuevo botón "↔️ Voltear" que da la vuelta a la imagen en espejo. Se respeta en la vista previa, el PNG, el Word con imágenes y el Word con texto (en este último se "hornea" en los píxeles, ya que Word no interpreta bien el `transform` de CSS).
- **Sección "IA" reorganizada**: ahora vive plegada por defecto dentro de "Ilustración" (ya que no se usa a diario), y se divide en dos bloques:
  - **Generar con IA**: el generador de bocetos por texto que ya existía (Pollinations.ai, gratuito, experimental).
  - **Editar con IA — "Blanquear fondo"**: nueva herramienta que detecta el color de las cuatro esquinas de la imagen y convierte a blanco los píxeles parecidos, con un margen de tolerancia ajustable. **Aviso honesto**: esto NO es una segmentación real por IA (separar la miniatura del fondo de verdad requeriría un servicio de pago o un modelo de aprendizaje pesado, entrenado además para personas/retratos y no para miniaturas de mesa). Es un ajuste automático simple que funciona razonablemente si la foto ya tiene un fondo bastante liso o claro; con fondos complejos no dará buen resultado. Se ha descartado el difuminado selectivo de fondo por la misma razón: sin saber qué es "miniatura" y qué es "fondo" con precisión, no se puede difuminar solo una parte.
- **Crear fichas por tipo, con iconos**: sustituido el interruptor "Hoja de unidad / Hoja de personaje" (que cambiaba el tipo de la ficha activa) por dos botones directos en "Tus fichas": "📜 Nueva unidad" y "🧙 Nuevo personaje", cada uno crea una ficha en blanco de ese tipo. *Nota: con este cambio se pierde la posibilidad de convertir una ficha ya existente de un tipo a otro; si en el futuro hace falta, se puede recuperar como una acción aparte.*
- **Arrastrar solo desde el icono ⠿**: en Perfiles de características y Opciones, ahora solo se puede reordenar agarrando el icono de puntos, no la fila entera. Antes, intentar seleccionar texto dentro de una casilla a veces se interpretaba como "quiero arrastrar la fila", interrumpiendo la selección. Aplicado también a "Tus fichas" por consistencia.
- **Fichas nuevas se insertan tras la activa**: al crear una ficha (de cualquiera de los dos tipos), aparece justo después de la que tenías abierta, en vez de al final de la lista.
- **Escudo por ficha**: el escudo del ejército sigue siendo el valor por defecto para todas las fichas, pero ahora cada ficha puede tener el suyo propio si se sube uno específico (opción "Reemplazar solo en esta ficha", dentro de "Escudo del ejército"). Un botón permite volver a usar el del ejército en cualquier momento.
- **Aviso de archivo cargado / cambios sin guardar**: aparece una línea discreta bajo el título del panel mostrando el nombre del último archivo cargado o guardado, y si hay cambios sin guardar (● cambios sin guardar / ✓ guardado). Además, si intentas cerrar la pestaña con cambios pendientes, el navegador te avisa.
- **Changelog en archivo aparte**: el historial de versiones se traslada a este `CHANGELOG.md`. El comentario dentro del `.html` se queda solo con el número de versión actual, para no duplicar el contenido en dos sitios.

---

## v2.12
Panel lateral izquierdo un 30% más ancho por defecto (400px → 520px). Sigue siendo redimensionable arrastrando el borde, igual que antes.

## v2.11
Arreglado un fallo por el que los valores de los perfiles se salían del recuadro (las casillas no se encogían a su columna de la rejilla); aplicado también a Opciones y Montura por si acaso. Reglas especiales admite ahora saltos de línea: dentro de cada línea las reglas separadas por comas se siguen juntando igual, pero se puede pulsar Intro para maquetar el texto en varias líneas (por ejemplo, alrededor de la ilustración). Se respeta en la vista previa, el PNG, el Word con texto, el Word con imágenes y el resumen.

## v2.10
Revertido el atributo "TS" añadido en la v2.8 (perfiles vuelven a ser M, HA, HP, F, R, H, I, A, L). Afecta al panel, la vista previa, el Word individual, el PNG y el resumen. Se añadió además una limpieza automática por si alguna ficha se llegó a guardar con el TS durante ese tiempo, para que no quedaran columnas descuadradas. Puede que se retome en el futuro.

## v2.9
Arreglado un fallo que a veces desplazaba la posición de la ilustración al cargar una ficha o cambiar entre fichas: se debía a que se intentaba encajar la imagen en los márgenes antes de conocer su tamaño real (se asumía cuadrada por error), lo que podía mover y guardar una posición equivocada. Ahora solo se ajusta una vez se conocen las medidas reales. El programa empieza vacío al abrirlo (como al pulsar "+ Nueva ficha"), en vez de traer una ficha de ejemplo ya rellena.

## v2.8
"Word con texto" ahora genera siempre un único documento con todas las fichas marcadas (antes exportaba una por archivo). Resumen de perfiles: columnas de atributos con el mismo ancho (usando colgroup), tipografía algo más cuidada y compacta, el primer perfil de cada ficha sale ligeramente más grande que los siguientes para distinguir dónde empieza cada unidad sin usar títulos, y las reglas especiales pasan a ser una tabla de dos columnas (Ficha / Reglas). Nuevo atributo "TS" al final de cada perfil de características (de momento vacío por defecto).

## v2.7
Nuevo botón "Quitar imagen" en Ilustración. Arreglado el brillo en la exportación a PNG (y Word con imágenes): ahora se hornea en los píxeles antes de capturar, en vez de depender de que html2canvas interprete el filtro CSS (no lo hacía de forma fiable). Rehecho el "Word con texto": vuelve a ser una tabla de dos columnas de verdad (texto a la izquierda, imagen arriba a la derecha) en vez de posición libre, que Word no soportaba bien y hacía que la imagen cayera al final; de paso, las columnas de atributos tienen ahora el mismo ancho. Rehecho el resumen de perfiles: página 1 con todos los atributos en una tabla compacta sin títulos de unidad, página 2 con las reglas especiales (una línea por ficha).

## v2.6
Todas las secciones del panel se pueden plegar/desplegar ahora (antes solo Vista y Escudo del ejército). Solo esas dos siguen empezando cerradas por defecto; el resto empiezan abiertas.

## v2.5
El programa pasa a llamarse CodexMaker (título de la pestaña y cabecera del panel). Cuando no hay ilustración subida, el hueco se queda en blanco (sin texto ni borde de aviso).

## v2.4
Las secciones "Vista" y "Escudo del ejército" ahora se pueden plegar/desplegar (empiezan plegadas, ya que se usan poco). Nuevos botones en "Tus fichas": "Vaciar ficha actual" (borra el contenido de la ficha abierta sin quitarla de la lista) y "Borrar todas las fichas" (deja el archivo como si fuera nuevo). Ambos piden confirmación antes de actuar, ya que no se pueden deshacer.

## v2.3
El "Alto máximo de la ficha" ahora es un ajuste propio de cada ficha (antes era global para todo el archivo), con un rango más estrecho de 450 a 800 px. Como es un máximo, no un tamaño fijo, las fichas con poco texto quedan más pequeñas solas, mientras que las que llenan ese espacio se quedan en el tamaño estándar (800).

## v2.2
"Quitar marco" pasa a ser el ajuste por defecto. Montura se muestra ahora como lista con viñetas (una por línea), igual que Opciones, y sigue sin aparecer nada si no hay ninguna montura. Nuevo control "Alto máximo de la ficha" en Vista: si el contenido no cabe, se recorta por abajo (nunca por arriba); pensado para que quepan varias fichas en la misma página al exportar a Word con imágenes o PNG.

## v2.1
Hoja de personaje: Montura pasa a ir debajo de Equipo, y solo se muestra si tiene algo (antes, de meter una montura, ya salía vacía). Word con imágenes: las imágenes salen centradas, con menos margen de página, y ya no se fuerza una página por ficha, así que si dos caben en la misma hoja, Word las coloca juntas automáticamente. Los tres botones de exportar caben ahora en una sola línea (con texto más corto). Recuperado el reordenar fichas arrastrando en "Tus fichas" (icono ⠿).

## v2.0
Nuevo interruptor "Quitar/Añadir marco" en Vista: controla si la ficha lleva el borde exterior, tanto en la vista previa como en todas las exportaciones (PNG, Word con texto, Word con imágenes). Ajustado el espaciado entre fichas en "Tus fichas" para que se vea más compacto, como antes.

## v1.9
Nuevas opciones de exportación: PNG (imágenes sueltas), Word con texto (como antes) y Word con imágenes (todas las fichas marcadas pegadas como imágenes dentro de un único documento, una tras otra). Se quitó la exportación a PDF. Al combinar varias fichas, los personajes van siempre primero y las unidades después. Añadidas de verdad las hojas de personaje (antes solo un aviso de "próximamente"): son iguales a las de unidad salvo que no llevan "Tamaño de la unidad" y en su lugar tienen una lista de "Montura" (nombre + puntos, se pueden añadir varias).

## v1.8
"Exportar fichas" ya no es una sección aparte: la casilla de selección para exportar vive ahora en cada fila de "Tus fichas", junto con marcar/desmarcar todas y los botones de Word/PDF.

## v1.7
Reordenado el panel: Guardar/cargar, Vista, Escudo del ejército, Tus fichas y Exportar fichas ahora van arriba del todo, en ese orden, seguidos del resto de secciones como antes. Eliminado el botón "Descargar como imagen (PNG)" (ya no era necesario).

## v1.6
Nuevo control de brillo para la ilustración (más clara o más oscura), aplicado en la vista previa, el PNG, el PDF y el Word.

## v1.5
Nueva exportación con lista de comprobación: eliges qué fichas exportar (marcar/desmarcar todas), y a qué formato, Word o PDF. Si marcas varias, se descargan juntas en un `.zip` (un archivo por ficha); si solo marcas una, se descarga directamente. Sustituye a los antiguos botones de "una ficha" / "todas las fichas".

## v1.4
Arreglo: la generación de imágenes con IA usaba un endpoint que requiere autenticación y fallaba siempre; ahora usa el endpoint clásico y realmente gratuito de Pollinations.ai, y muestra las imágenes que sí se generen aunque alguna falle.

## v1.3
Nueva opción para generar imágenes de referencia con IA a partir del nombre de la unidad (fondo blanco, 3 variantes), usando el servicio externo gratuito Pollinations.ai (sin clave API). Experimental.

## v1.2
El título y la barra gris siempre quedan por delante de la ilustración, aunque se suba por encima del borde del texto (antes se veía la imagen tapando esa zona).

## v1.1
La ilustración se puede subir un poco por encima del borde superior del texto (hasta 40px), y empieza colocada así por defecto.

## v1.0
Primera versión "estable": fichas múltiples por archivo, perfiles de características, grupo de mando, opciones, reglas especiales, escudo de ejército único, ilustración de tamaño y posición libres (nunca se recorta, se puede colocar delante o detrás del texto), vista y exportación en blanco y negro, exportación a PNG y a Word (ficha suelta, documento con todas las fichas en páginas separadas, y resumen de perfiles), guardar/cargar todo el archivo en `.json` con imágenes comprimidas automáticamente, panel redimensionable.
