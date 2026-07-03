# CHANGELOG — CodexMaker

Generador de fichas de unidad y personaje (Warhammer). Este archivo recoge el historial completo de cambios, versión a versión. A partir de ahora se mantiene aparte del `.html` para poder adjuntarlo o consultarlo sin abrir el programa.

Para un versionado real (poder volver atrás con garantías), usa Git: haz un commit cada vez que subas una versión nueva a tu repositorio, y opcionalmente crea un tag (`v1.0`, `v1.1`...) para marcar los hitos importantes.

---

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
