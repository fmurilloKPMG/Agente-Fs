---
name: preparacion-cumplimentacion-fichas
description: "Use this skill when, in Preparación de fichas and only after CONTROL PREVIO INFORME SUPERADO, the text of each subapartado F of the standard report (IAGC) must be copied verbatim into its own ficha técnica built from the knowledge template Plantilla_Ficha_F_SCIIF.xlsx: fills the apartado title, the blue box with the code and question, and the yellow part of Table1 (Descripción efectuada por la Sociedad), one row per internal title with the title in column A and its paragraphs in the yellow column, adding rows without breaking the table or the template («rellenar la parte amarilla», «volcar el texto del informe en las fichas», «cumplimentar fichas F.1.1, F.1.2…», «preparar las fichas del apartado F»). It returns one Excel per ficha. Do not use it to check the files (preparacion-control-previo-informe), to fill Referencia a la documentación soporte or Comentario, to review against evidence (Fichas técnicas), nor for actas (Mapeo de actas)."
metadata:
  version: "1.2"
---

# Cumplimentación de fichas desde el informe · Preparación de fichas

Construye una ficha por subapartado del apartado F a partir de la plantilla limpia del conocimiento y copia en ella, tal cual, lo que dice el informe estándar: el título del apartado, el código y la pregunta del subapartado (recuadro azul) y el texto de respuesta (parte amarilla de `Table1`). Dentro de cada ficha, cada título interno ocupa una fila nueva de `Table1`: el título va en la columna A y sus párrafos en la columna amarilla. El resultado son fichas listas para Fichas técnicas.

## Resources
| Resource | Type | Location | Read / use at | If missing |
|---|---|---|---|---|
| `Plantilla_Ficha_F_SCIIF.xlsx` | Template | Agent knowledge | Fase 4, una copia por ficha | Detente y avisa: las fichas se construyen siempre desde esta plantilla |
| `references/guia-insercion-filas-table1.md` | Reference | This skill | Fase 4, antes de escribir en la ficha | Detente y avisa: sin la guía no se garantiza que la tabla no se rompa |

## Workflow

### Fase 1 · Comprobar el punto de partida
Comprueba que en la conversación hay un «CONTROL PREVIO INFORME: SUPERADO» con la tabla de fichas a preparar. Si no lo hay, indica que hay que ejecutar antes `preparacion-control-previo-informe`.

### Fase 2 · Extraer el texto de cada subapartado
Para cada ficha a preparar, localiza en el informe su subapartado y extrae:
1. **Título del apartado:** el código del apartado sin el punto final y su título tal cual (p. ej. «F.1» y «Entorno de control de la entidad.»).
2. **Código y pregunta del subapartado:** el código (p. ej. «F.1.1») y el texto completo de la pregunta, tal cual, uniendo las líneas cortadas por el ancho de página. Si el apartado no tiene subapartados (p. ej. F.6), el código es el del apartado y la pregunta es su título.
3. **Títulos internos:** cada punto marcado con «·» bajo la pregunta del subapartado. Copia el texto del título completo, tal cual, sin el «·» del margen. Si el título empieza por otro símbolo propio del texto (p. ej. «• Canal de denuncias…»), ese símbolo se conserva.
4. **Párrafos de respuesta:** el texto del recuadro que sigue a la pregunta o a cada título, hasta el siguiente título, el siguiente subapartado o el siguiente apartado.
5. **Agrupación:** cada título con sus párrafos forma un bloque. Si el subapartado no tiene títulos internos, hay un único bloque con el título vacío.

Reglas de transcripción, todas obligatorias:
- **Literal:** mismas palabras, mayúsculas, tildes, signos, cifras, comillas y viñetas («-», «–», «•»). No se corrigen erratas ni se cambia nada (si el informe dice «SCIINF» o «auditoria», se copia así).
- **Párrafos:** un párrafo es el texto entre dos líneas en blanco del informe. Las líneas de un mismo párrafo cortadas solo por el ancho de página se unen con un espacio. Los párrafos se separan con una línea en blanco (dos saltos de línea).
- **Saltos dentro de un párrafo:** se conserva un salto de línea simple cuando el informe empieza una línea nueva sin línea en blanco (p. ej. un elemento de lista «- …» o una frase que arranca en línea nueva tras una línea corta).
- **Cambio de página:** el texto que continúa en la página siguiente forma parte del mismo subapartado. No copies cabeceras, pies ni números de página. La pregunta del subapartado va solo en el recuadro azul, nunca en la parte amarilla.
- Sin espacios al final de las líneas.

### Fase 3 · Confirmación del usuario
Muestra, por ficha: código, título del apartado, inicio de la pregunta, nº de bloques y, para cada bloque, el título (o «sin título»), el nº de párrafos y las primeras y últimas palabras del texto. Pregunta si se puede rellenar. No escribas en ninguna ficha hasta recibir la confirmación. Si el usuario corrige algo, aplícalo y vuelve a mostrar el resumen.

### Fase 4 · Construir y rellenar cada ficha
Lee `references/guia-insercion-filas-table1.md`. Después, para cada ficha:
1. **Partir de la plantilla:** haz una copia de `Plantilla_Ficha_F_SCIIF.xlsx` del conocimiento. Nunca uses una ficha adjunta ni una ficha ya rellenada como base.
   - Renombra la hoja «F.x.x» con el código del subapartado (p. ej. «F.1.1»).
   - B8: código del apartado, un espacio y su título (p. ej. «F.1 Entorno de control de la entidad.»).
   - B10, recuadro azul: código del subapartado, un espacio y la pregunta completa (p. ej. «F.1.1 Qué órganos y/o funciones son los responsables de: …»).
   - En el texto de «Lectura y entendimiento» (fila 21 de la plantilla), sustituye «F.x.x» por el código del subapartado.
2. **Ajustar filas:** debe haber una fila de datos en `Table1` por cada bloque.
   - Si faltan filas, se insertan justo debajo de la última fila de la tabla, con el mismo formato (relleno amarillo incluido). Se amplían el rango de `Table1` y su autofiltro, y se desplaza hacia abajo todo lo que hay debajo, con sus alturas y celdas combinadas.
   - Si sobran filas, se dejan vacías; no se eliminan.
3. **Escribir**, en el orden del informe:
   - Columna A: el título del bloque, o vacía si no tiene título.
   - Columna amarilla: el texto del bloque.
   - Ajuste de texto y alineación superior; altura de fila estimada según el texto, con un máximo de 409 puntos.
4. **No tocar nada más:** ni la cabecera (código, entidad, fecha), ni las instrucciones del modelo (recuadro azul inferior), ni los requisitos y sus marcas, ni el trabajo realizado, ni los anchos de columna. No se añaden hojas ni paneles inmovilizados.

### Fase 5 · Guardar y comprobar
1. Guarda cada ficha como `[código]_Procedimiento_[ejercicio].xlsx` (p. ej. `F.1.1_Procedimiento_2025.xlsx`), con la misma extensión que la plantilla, y entrégala al usuario.
2. Aplica la comprobación posterior de la guía: texto idéntico al extraído, B8 y B10 correctos, rango de `Table1` correcto y el resto de celdas igual que en la plantilla. Corrige cualquier diferencia antes de entregar.

## Output
1. Un Excel por ficha, con el nombre de la fase 5.
2. En el chat:
   - tabla `Ficha | Bloques (filas de Table1) | Títulos | Filas añadidas | Observaciones`;
   - avisos: filas cuyo texto supera la altura máxima visible de Excel (409 puntos) y subapartados sin texto de respuesta;
   - siguiente acción sugerida: «Las fichas están listas para Fichas técnicas».

## Rules
- Solo se escribe en el nombre de la hoja, B8, B10, el código de la fila de «Lectura y entendimiento», la columna A y la columna amarilla de `Table1`; Referencia y Comentario quedan vacías para Fichas técnicas.
- El texto se copia literal: no se resume, no se corrige, no se reordena y no se completa.
- Una ficha por subapartado: nunca se mezcla texto de dos subapartados en la misma ficha.
- Nunca se sobrescriben filas de debajo de la tabla: se insertan filas.
- No se rellena una ficha sin la confirmación de la fase 3.
- No se inventa texto: si un bloque no tiene párrafos en el informe, la celda queda vacía y se avisa.

## Flow position
- Flow: Preparación de fichas
- Previous: `preparacion-control-previo-informe` – receives: CONTROL PREVIO INFORME: SUPERADO con entidad, ejercicio y tabla de fichas a preparar
- Next: none – hands over: las fichas Excel cumplimentadas y el resumen en el chat; pueden aportarse después en Fichas técnicas
- Advance when: el usuario confirma el resumen de la fase 3

## Failure handling
- No hay CONTROL PREVIO INFORME: SUPERADO: envía al usuario a `preparacion-control-previo-informe`.
- El texto extraído contiene símbolos sin sentido: detente; el informe no es legible y hay que volver al control previo.
- Duda sobre dónde acaba un subapartado o si una línea es un título: muéstralo en el resumen de la fase 3 y pregunta.
- Falta la guía técnica o la plantilla del conocimiento: detente y avisa.
- La comprobación de la fase 5 falla y no se puede corregir: no entregues esa ficha y explica el motivo.

## Final validation
- Cada ficha tiene tantas filas de datos en `Table1` como bloques tiene su subapartado.
- El texto de cada fila es idéntico al del informe y el título está en la columna A.
- B8 y B10 contienen el título del apartado y el código con la pregunta, tal cual.
- La hoja se llama como el subapartado, Referencia y Comentario están vacías y no hay hojas añadidas.
- El resto de celdas es igual que en la plantilla.
- El nombre del archivo sigue el patrón de la fase 5.
