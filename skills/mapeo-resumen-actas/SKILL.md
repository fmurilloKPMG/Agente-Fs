---
name: mapeo-resumen-actas
description: "Use this skill when, inside Mapeo de actas and after the completitud check, the matrix must be filled: one row per acta in the Plantilla Actas_Fs AGENTE IA, columns B to K (nombre del documento, acta, órgano, fecha, asistentes, puntos del día, acuerdos adoptados, otros asuntos tratados, firmantes, impacto en EEFF), either reading the actas or copying literally the client's Excel «Análisis y resumen de actas» or a matrix given by the user («resumir actas», «volcar el Excel del cliente», «rellenar la plantilla»). Do not use it to fill the column Referencia con Sub-apartado F (mapeo-cruce-actas-sciif), to review or emit the Excel (mapeo-control-calidad-actas), nor for Fichas técnicas."
metadata:
  version: "2.2"
---

# Resumen de actas · Mapeo de actas

Completa una fila por acta en la matriz de referencia, solo en las columnas que tiene. Los campos con más peso son fecha, puntos del día, acuerdos adoptados y otros asuntos tratados, porque son la base de la referencia con el apartado F.

## Resources
| Resource | Type | Location | Read / use at | If missing |
|---|---|---|---|---|
| `Plantilla Actas_Fs AGENTE IA.xlsx` | Template | Agent knowledge | Punto 1 del workflow | Detente y avisa: no se puede cumplimentar sin la matriz de referencia |

## Estructura de la matriz (hoja «Plantilla Resumen ACTAS»)
Cabecera: C5 = ID del procedimiento · E16 = Nombre del cliente · E17 = Fecha de cierre. «Preparado por» y «Revisado por» los completa el equipo. Cabeceras de columna en la fila 23; datos desde la fila 24, una fila por acta.

| Col. | Cabecera | Quién la rellena |
|---|---|---|
| B | Nombre del documento | Esta skill |
| C | ACTA CONSEJO ADMON / JUNTA GENERAL ACC / COMITÉ DE AUDITORIA… | Esta skill |
| D | Órgano (CAC / CONSEJO DE ADMINISTRACIÓN…) | Esta skill |
| E | FECHA ACTA | Esta skill |
| F | ASISTENTES | Esta skill |
| G | PUNTOS DEL DÍA | Esta skill |
| H | ACUERDOS ADOPTADOS | Esta skill |
| I | OTROS ASUNTOS TRATADOS | Esta skill |
| J | FIRMANTES | Esta skill |
| K | Impacto en EEFF | Esta skill |
| L | Referencia con Sub-apartado F | `mapeo-cruce-actas-sciif` |

No añadas, quites, muevas ni renombres columnas, hojas o cabeceras.

## Textos estándar (literales)
| Situación | Texto |
|---|---|
| Dato que no aparece en el acta | `No consta en el acta. Solicitar al cliente: [dato].` |
| No hay otros asuntos tratados | `No constan.` |
| Acta no firmada | `Acta sin firmar (borrador).` |
| No hay hechos con impacto en EEFF | `A completar por auditoría.` |

## Workflow
1. Abre `Plantilla Actas_Fs AGENTE IA.xlsx` del conocimiento y trabaja sobre una copia. Escribe la cabecera (C5, E16, E17).
2. Según el modo de trabajo del control previo, aplica la sección A (solo actas) o la sección B (Excel del cliente o matriz del usuario).
3. Ordena las filas por órgano y, dentro de cada órgano, por fecha. Con el Excel del cliente, respeta su orden.

### A · Solo actas: resumen de cada acta
- **B · Nombre del documento:** nombre exacto del archivo; si es una sesión de un documento con varias actas, añade «(sesión dd/mm/aaaa)».
- **C · Acta:** «Acta del [órgano] de dd/mm/aaaa»; si es parcial, «Acta parcial del [órgano] de dd/mm/aaaa».
- **D · Órgano:** el normalizado del inventario.
- **E · Fecha acta:** fecha de la reunión, en formato dd/mm/aaaa.
- **F · Asistentes:** nombre y cargo si consta, con esta estructura: `Presidente: …` / `Secretario: …` / `Miembros:` con una línea por persona e indicación de «(asiste telemáticamente)» o «(representado por X)» / `Invitados:` con cargo y, si aplica, «(para el punto n)».
- **G · Puntos del día:** orden del día numerado, literal y en el orden del acta.
- **H · Acuerdos adoptados:** un bloque por punto con acuerdo: `Punto [n]: [acuerdo fiel al texto, con importes, plazos y personas]. Votación: [unanimidad | mayoría, con abstención de X | voto en contra de X | no consta].`
  - El voto se recoge tal como lo dice el acta: si hay una abstención, **no** es unanimidad.
  - Incluye los acuerdos rutinarios (aprobación del acta anterior, delegación de facultades).
  - Punto sin acuerdo: `Punto [n]: sin acuerdo; se informa de [tema].` Punto no tratado: `Punto [n]: no consta tratado en el acta.`
- **I · Otros asuntos tratados:** lo que se informa o debate sin acuerdo (presentaciones de dirección, informes de comisiones, marcha del negocio). Las cifras van con su periodo («a abril 2025»), sin confundir datos reales con presupuesto. Si no hay ninguno: `No constan.`
- **J · Firmantes:** quién firma y en calidad de qué. Si no está firmada: `Acta sin firmar (borrador).`
- **K · Impacto en EEFF:** solo hechos con efecto económico que **consten literalmente** en el acta, con su importe, separados por «;» (deuda o refinanciación, capex, operaciones de capital, conversión de obligaciones, dividendos, operaciones vinculadas, indemnizaciones, actualización del presupuesto). Si no hay ninguno: `A completar por auditoría.` Nunca hagas valoraciones contables.

### B · Excel del cliente o matriz del usuario: copia literal
1. **Mapeo obligatorio** (cliente → matriz):

| Excel del cliente | Matriz |
|---|---|
| C5 · ID del procedimiento | C5 |
| D16 · Nombre del cliente | E16 |
| D17 · Fecha de cierre | E17 |
| B · Nombre del documento | B |
| C · Acta | C |
| — (no existe en el cliente) | D · Órgano, con el valor del inventario |
| D · Fecha acta | E |
| E · Asistentes | F |
| F · Puntos del día | G |
| G · Acuerdos adoptados | H |
| H · Otros asuntos tratados | I |
| I · Firmantes | J |
| J · Impacto en EEFF | K |
| K · Trabajo realizado equipo de auditoría | No se traslada: la matriz no tiene esa columna |

   Como el cliente no tiene «Órgano», desde la fecha cada columna del cliente cae una posición a la derecha. Comprueba en cada fila que los asistentes están en F, los acuerdos en H y el impacto en K. Si la matriz la aporta el usuario con las mismas columnas que la plantilla, la correspondencia es directa, columna a columna.
2. **Copia carácter a carácter**, con erratas, saltos de línea, viñetas y fechas. Una fila de origen = una fila de la matriz, en el mismo orden. No copies «Preparado por / Revisado por».
3. **Celdas vacías:** B a J → `No consta en el acta. Solicitar al cliente: [dato].`; I vacía → `No constan.` y anótalo para revisarlo; K vacía → `A completar por auditoría.` Las celdas que expresan ausencia («No constan firmas», «Sin asistentes mencionados») se copian literales y se anotan.
4. **Si además hay actas:** léelas con las reglas de la sección A **sin escribir el resultado en la matriz**; pásalo como contraste a `mapeo-control-calidad-actas`.

## Output
- La copia de la matriz con las columnas B a K cumplimentadas, una fila por acta; la columna L queda vacía para la siguiente skill.
- Trazabilidad interna por fila, que **no se escribe en el Excel**: origen de cada dato (cliente, acta, agente o no consta), página o fila de origen, observaciones y, si aplica, el contraste con el acta.

## Rules
- No inventes asistentes, acuerdos, fechas, importes ni firmantes.
- La información del cliente no se corrige, resume ni reordena.
- No rellenes la columna L ni hagas el control de calidad.

## Flow position
- Flow: Mapeo de actas
- Previous: `mapeo-completitud-actas` – receives: inventario confirmado, cuadro de completitud y puntos agrupados
- Next: `mapeo-cruce-actas-sciif` – hands over: la matriz con B a K cumplimentadas y su trazabilidad interna
- Advance when: todas las actas del inventario tienen su fila

## Failure handling
- Un acta se puede leer solo en parte: resume lo legible, marca el resto con «No consta en el acta» y anótalo.
- Una columna del cliente no encaja en el mapeo: no la fuerces; anótala para la revisión.
- Falta la matriz en el conocimiento: detente y avisa.

## Final validation
- Hay una fila por acta del inventario.
- Ninguna celda de B a K está vacía.
- Con Excel del cliente, el texto copiado es idéntico al original y no hay desplazamientos de columna.
- La estructura de la matriz no ha cambiado.
