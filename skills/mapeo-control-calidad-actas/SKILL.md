---
name: mapeo-control-calidad-actas
description: "Use this skill when Mapeo de actas reaches its end, after column L is filled, to review the matrix and the completitud, group every finding in a single executive tab Revisión y pendientes, emit the only Excel deliverable from the Plantilla Actas_Fs AGENTE IA with suffix _revisado and show the filled matrix on screen as a table («revisar resultado», «pendientes del cliente», «emitir Excel», «entregar matriz»). Do not use it to summarise actas (mapeo-resumen-actas), to link them to apartado F (mapeo-cruce-actas-sciif), nor for Fichas técnicas."
metadata:
  version: "2.2"
---

# Control de calidad y emisión · Mapeo de actas

Cierra el Mapeo de actas con un resultado sencillo de usar: la matriz cumplimentada y una sola pestaña adicional que, de un vistazo, dice qué falta, qué hay que pedir al cliente y qué debe revisar el equipo. No corrige el contenido del cliente ni rehace el resumen o el cruce.

## Resources
| Resource | Type | Location | Read / use at | If missing |
|---|---|---|---|---|
| `Plantilla Actas_Fs AGENTE IA.xlsx` | Template | Agent knowledge | Punto 5 | Detente y avisa: el entregable debe salir de esta matriz |

## Workflow

### 1. Revisión (con preguntas, sin códigos)
- **¿Tenemos todo?** ¿Están todas las actas del control y firmadas? ¿Hay actas parciales o solo con orden del día? ¿Hay celdas «No consta en el acta» o del cliente que indican que falta algo?
- **¿Es coherente cada acta?** ¿Hay puntos del día que parecen una decisión sin acuerdo? ¿Las personas de los acuerdos figuran como asistentes o invitados? ¿Hay nombres duplicados o escritos de forma distinta entre actas? ¿La fecha cuadra con el archivo y con la referencia a la reunión anterior? ¿Se presentan como presupuesto cifras reales, o al revés?
- **¿Coincide el resumen del cliente con el acta?** (solo si hay contraste) Sentido del voto; acuerdos del acta que faltan en el resumen; importes, fechas y nombres.
- **¿Está bien volcado?** Cada dato en su columna, sin desplazamientos; texto del cliente idéntico al original; estructura de la matriz intacta; la columna «Trabajo realizado» del cliente no se ha trasladado porque la matriz no la tiene.
- **¿Es fiable la referencia con el apartado F?** Líneas [A validar]; acuerdos u otros asuntos que encajan en el Crossref y no se han vinculado; líneas sin punto del acta.

### 2. Agrupación: una línea por punto
- Un mismo punto que afecta a varias actas va en **una sola línea** con todas las actas afectadas (Correcto: «Actas sin firmar: Consejo de Administración 25/02, 26/02 y 28/03.»; Incorrecto: una fila por acta).
- Lo que se resuelve con una misma acción sobre una misma acta va en una línea (p. ej. «Comisión de Auditoría 27/01, 24/02 y 18/03: solo se ha recibido el orden del día; pedir las actas completas y firmadas con sus asistentes.»).
- Los detalles de forma (erratas del cliente, tratamientos, orden de filas) van en **una única línea final informativa**.

### 3. Pestaña «Revisión y pendientes»
Una única pestaña que se lee en un minuto, con tres bloques:
1. **Resumen** (3–5 líneas de texto): entidad y fecha de cierre; «Se esperaban [n] actas según el control y se han recibido [n] ([n] firmadas, [n] en borrador). Faltan [n].»; «Hay [n] puntos que pedir al cliente y [n] que debe revisar el equipo.»; «[n] referencias con el apartado F quedan [A validar].»
2. **Completitud por órgano:** el cuadro de `mapeo-completitud-actas` (`Órgano | Reuniones según el control | Actas firmadas | Actas en borrador | Actas que faltan | Situación`).
3. **Puntos a resolver:** `Nº | Prioridad | Qué hemos detectado | Actas afectadas | Qué hay que hacer | Quién`.
   - Prioridad **Alta:** falta un acta, acuerdo omitido, voto distinto o error de volcado. **Media:** actas sin firmar, datos que no constan, actas parciales, referencias [A validar]. **Baja:** forma.
   - «Qué hemos detectado» y «Qué hay que hacer» en una frase clara cada uno. «Quién»: Cliente, Equipo GRC o Equipo de auditoría.
   - Orden: por prioridad y, dentro de cada una, primero lo que se pide al cliente.

### 4. Formato ejecutivo
- **Hoja de la matriz:** ajuste de texto y alineación superior en las filas de datos; relleno ámbar suave en «No consta en el acta»; azul claro en el contenido generado por el agente (D · Órgano y L · Referencia con Sub-apartado F); gris claro en «A completar por auditoría».
- **«Revisión y pendientes»:** título en negrita; títulos de bloque destacados; tablas con encabezado en negrita y relleno, bordes finos, ajuste de texto y anchos legibles; prioridad con rojo suave (Alta), ámbar (Media) o gris (Baja); leyenda de colores al pie.
- **Sin paneles inmovilizados en ninguna hoja** (`freeze_panes = None`) y sin filtros que oculten filas.

### 5. Emisión de un único Excel
1. Parte siempre de una copia de `Plantilla Actas_Fs AGENTE IA.xlsx` del conocimiento, tanto si los datos vienen de las actas como de una matriz aportada, respetando su estructura, hojas, columnas y cabeceras.
2. Debe haber solo dos pestañas: la de la matriz («Plantilla Resumen ACTAS») y «Revisión y pendientes».
3. Nombre: `Plantilla Actas_Fs AGENTE IA_[entidad]_revisado.xlsx`. Es el **único** archivo que se entrega.

### 6. Matriz en pantalla
Muestra en el chat la matriz cumplimentada en formato tabla, con las mismas columnas y en el mismo orden que la matriz de referencia (B a L), una fila por acta y **sin omitir ni resumir el contenido de ninguna celda**. Si es muy larga, divídela en varios mensajes consecutivos sin recortar celdas. Después, el resumen ejecutivo (máximo 6 líneas) y los puntos de prioridad alta, una frase cada uno.

## Output
- `Plantilla Actas_Fs AGENTE IA_[entidad]_revisado.xlsx` con «Plantilla Resumen ACTAS» y «Revisión y pendientes».
- En el chat: la matriz en tabla, el resumen ejecutivo y los puntos de prioridad alta.

## Rules
- Nunca corrijas una celda del cliente: describe el punto.
- No inventes valores «según el acta» sin haber leído el acta.
- No uses códigos internos: todo se explica con palabras.
- No añadas columnas ni pestañas distintas de las indicadas.
- No concluyas sobre si la evidencia es suficiente para el apartado F.

## Flow position
- Flow: Mapeo de actas
- Previous: `mapeo-cruce-actas-sciif` – receives: la matriz con la columna L cumplimentada, el recuento de vinculaciones, el cuadro de completitud y la trazabilidad interna
- Next: none – hands over: el Excel `_revisado` y la matriz en pantalla
- Advance when: la comprobación final está superada

## Failure handling
- La columna L está vacía en alguna fila: indica que hay que ejecutar antes `mapeo-cruce-actas-sciif` para esas filas.
- Una comprobación final falla: corrige la emisión, nunca el dato del cliente, y repite la comprobación.
- No se puede generar el Excel: entrega la matriz y la pestaña de revisión en el chat y avisa.

## Final validation
- La matriz conserva su estructura (columnas B a L) y no tiene columnas añadidas.
- Hay una fila por acta y ninguna celda de B a L está vacía; el contenido del cliente es idéntico al original.
- La columna L tiene una línea por subapartado o «Sin vinculación con el apartado F.».
- Solo existen las pestañas «Plantilla Resumen ACTAS» y «Revisión y pendientes», sin paneles inmovilizados, y ningún punto se repite.
- El archivo tiene el sufijo `_revisado` y la matriz se ha mostrado completa en pantalla.
