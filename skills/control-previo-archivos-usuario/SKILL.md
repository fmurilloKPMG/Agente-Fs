---
name: control-previo-archivos-usuario
description: "Use this skill when the user starts Fichas técnicas, or re-attaches files in it, and the ficha técnica Excel of apartado F (SCIIF, IAGC) and its documentación soporte must be checked before the review: «Fichas técnicas», «revisar ficha técnica», «validar archivos», «control previo». It checks only files, readability, Table1 and its columns, and identifies paragraphs P1, P2, P3. It returns CONTROL PREVIO SUPERADO or NO SUPERADO with correction steps. Do not use it for the review itself or to fill the ficha (cumplimentacion-ficha-tecnica-f-sciif), nor for actas (Mapeo de actas)."
license: Proprietary
metadata:
  version: "2.2"
---

# Control previo de archivos · Fichas técnicas

Comprueba, antes de la revisión, que el usuario ha aportado una ficha técnica Excel del apartado F con una tabla `Table1` válida y al menos un documento soporte legible. El resultado solo puede ser `CONTROL PREVIO: SUPERADO` o `CONTROL PREVIO: NO SUPERADO`. Es un control exclusivamente técnico: no revisa evidencias ni modifica archivos.

## Workflow

### 1. Identificación de archivos
1. Identifica cuál de los archivos aportados es la ficha técnica Excel del apartado F (`.xlsx` o `.xlsm`).
2. Considera el resto como documentación soporte. No uses un Excel soporte como ficha.
3. Comprueba que hay una única ficha y al menos un documento soporte.

### 2. Legibilidad
1. Verifica que la ficha se puede abrir.
2. Verifica que cada documento soporte se puede abrir y tiene texto legible.
3. Identifica los archivos protegidos, dañados, vacíos, incompatibles o escaneados sin texto recuperable.
4. No analices todavía si las evidencias sustentan los párrafos.

### 3. Validación de Table1
1. Abre la ficha con `openpyxl.load_workbook(ruta_original)`; para `.xlsm`, usa `keep_vba=True`.
2. Recorre todas las hojas y busca en `ws.tables` una tabla con el nombre exacto `Table1`. No aceptes como sustituto un rango con cabeceras parecidas, y no crees, renombres ni reconstruyas la tabla.
3. Comprueba que `Table1` tiene estas tres columnas: `Descripción efectuada por la Sociedad`, `Referencia a la documentación soporte` y `Comentario`.
4. Comprueba que tiene al menos una fila de datos y que la columna de descripción contiene texto.

### 4. Identificación de párrafos
1. Recorre las celdas no vacías de `Descripción efectuada por la Sociedad`.
2. Normaliza los saltos `\r\n` y `\r` a `\n` y separa el contenido por una o varias líneas en blanco.
3. Cada bloque no vacío es un párrafo. Numéralos de forma global y en orden de lectura: `P1`, `P2`, `P3`… Una celda puede contener varios párrafos.
4. Conserva la relación entre identificador, hoja, celda, fila y texto. No modifiques, unas, resumas ni reescribas los párrafos.

### 5. Cierre del control
El control termina aquí. No analices, valores ni cuestiones el contenido del cliente. Que un párrafo mencione documentos, políticas, artículos, apartados o códigos **nunca** es motivo de NO SUPERADO: esa comprobación corresponde a la cumplimentación.

## Motivos de NO SUPERADO (lista cerrada)
- Falta la ficha Excel.
- Falta la documentación soporte.
- Hay más de una posible ficha y no se puede identificar la correcta.
- Un archivo obligatorio no se puede abrir o leer.
- No existe una tabla con el nombre exacto `Table1`.
- Faltan columnas obligatorias.
- `Table1` está vacía o no tiene descripción.

Ningún otro aspecto (redacción, estilo, referencias documentales, menciones normativas) puede bloquear el proceso.

## Output

### Control superado
```text
CONTROL PREVIO: SUPERADO

Los archivos cumplen los requisitos necesarios para iniciar la revisión.

Ficha identificada: [nombre del archivo]
Ubicación de Table1: [hoja y rango]
Párrafos identificados: [número total]
Documentación soporte recibida: [relación breve]

El control previo ha finalizado correctamente. Los archivos ya pueden utilizarse en la siguiente fase de revisión.
```

### Control no superado
Agrupa todas las incidencias en un único mensaje, sin apartados vacíos:
```text
CONTROL PREVIO: NO SUPERADO

No hemos podido completar la validación porque necesitamos corregir algunos aspectos de los archivos. Te indicamos a continuación qué debes ajustar para poder continuar.

1. [Qué se ha detectado, en una frase]
   Qué tienes que hacer: [instrucción concreta].
   Dónde corregirlo: [archivo, hoja, tabla o columna, solo si es necesario].

2. [Siguiente incidencia, si existe]
   Qué tienes que hacer: [instrucción concreta].

Cuando hayas realizado estos cambios, vuelve a adjuntar los archivos y repetiremos el control previo.
```

### Instrucción según el motivo
| Motivo | Qué decir al usuario |
|---|---|
| Falta la ficha | No se ha identificado la ficha técnica; adjúntala en `.xlsx` o `.xlsm`. |
| Más de una ficha | Nombra los archivos candidatos y pide una única ficha, claramente diferenciada de la documentación soporte. |
| Falta documentación soporte | Se ha recibido la ficha, pero no las evidencias; pide al menos un documento soporte. |
| Archivo ilegible | Nombra el archivo y pide una copia accesible, no protegida y con texto legible. |
| No existe Table1 | «Abre el Excel, selecciona el rango correspondiente, conviértelo en una tabla y asigna a la tabla el nombre exacto Table1. Guarda el archivo y vuelve a adjuntarlo.» Muestra las hojas y tablas encontradas. |
| Faltan columnas | Muestra las cabeceras encontradas e indica las tres obligatorias. |
| Tabla vacía | Pide la ficha con la descripción cumplimentada en `Descripción efectuada por la Sociedad`. |

## Rules
- Tono profesional, cercano y ejecutivo; frases cortas, sin trazas técnicas, código ni nombres de funciones.
- No responsabilices al usuario. Si hay un solo error, muestra solo ese; si hay varios, muéstralos todos juntos.
- No declares el control superado si falla cualquier requisito. Si los requisitos técnicos están bien, decláralo superado aunque la descripción cite documentos.
- No modifiques archivos, no corrijas `Table1` ni sus cabeceras, y no pidas que se eliminen o trasladen referencias de ningún párrafo.
- No evalúes evidencias, no asignes estados de cumplimiento y no generes un Excel revisado.
- No inicies la revisión: esa es la siguiente skill del flujo.

## Flow position
- Flow: Fichas técnicas
- Previous: none – receives: los archivos adjuntos por el usuario
- Next: `cumplimentacion-ficha-tecnica-f-sciif` – hands over: el mensaje CONTROL PREVIO: SUPERADO con ficha, ubicación de Table1, nº de párrafos P1… y relación de documentación soporte
- Advance when: el resultado es CONTROL PREVIO: SUPERADO

## Failure handling
- Archivos que no se pueden abrir: trátalos como motivo de NO SUPERADO e indica cuáles.
- Duda sobre cuál es la ficha: no elijas; trátalo como «Más de una ficha».
- El usuario vuelve a adjuntar o sustituye archivos: ejecuta de nuevo el control completo.

## Final validation
- El resultado es exactamente una de las dos cabeceras de control.
- Cada NO SUPERADO responde a un motivo de la lista cerrada.
- No se ha modificado ningún archivo.
- En SUPERADO figuran ficha, ubicación de Table1, nº de párrafos y documentación soporte.
