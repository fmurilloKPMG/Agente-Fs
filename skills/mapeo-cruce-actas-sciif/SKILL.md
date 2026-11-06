---
name: mapeo-cruce-actas-sciif
description: "Use this skill when, inside Mapeo de actas and after the actas are summarised in the matrix, each acta must be linked; it links each acta with the subapartados F.1.1 to F.5.2 of the SCIIF: compares fecha, puntos del día, acuerdos adoptados and otros asuntos tratados with the Crossref de actas and its definitions, and fills column L Referencia con Sub-apartado F with one executive line per subapartado («cruce con el apartado F», «vincular actas», «referencia subapartado F», «crossref»). Do not use it to summarise actas or fill columns B to K (mapeo-resumen-actas), to review or emit the Excel (mapeo-control-calidad-actas), nor for Fichas técnicas."
metadata:
  version: "2.2"
---

# Cruce de actas con el apartado F · Mapeo de actas

Indica para cada acta, en pocas líneas, con qué subapartados del apartado F se relaciona y por qué. Es el resultado clave de la revisión GRC y después sirve de soporte en las fichas técnicas (Fichas técnicas). Escribe solo en la columna L.

## Resources
| Resource | Type | Location | Read / use at | If missing |
|---|---|---|---|---|
| `references/crossref-actas-apartado-f.md` | Reference | This skill | Punto 1 | Detente y avisa: sin el Crossref no se puede cumplimentar la columna L |

## Entradas
Por cada fila de la matriz se comparan cuatro columnas con el Crossref:

| Columna | Para qué se usa |
|---|---|
| E · Fecha acta | Situar el tema en el ejercicio y citar la sesión cuando un documento agrupa varias actas. |
| G · Puntos del día | Saber qué temas estaban previstos. |
| H · Acuerdos adoptados | Saber qué se ha decidido. Es la fuente principal. |
| I · Otros asuntos tratados | Recoger lo que se informa sin acuerdo: informes de Auditoría Interna, del auditor externo o de la dirección financiera. |

## Workflow
1. Lee `references/crossref-actas-apartado-f.md` completo: «Tema tratado», «Definición», señales típicas y temas sin vinculación. No continúes hasta haberlo leído.
2. Para cada acta, lee por este orden los acuerdos (H), los puntos del día (G), los otros asuntos (I) y la fecha (E), y separa los temas tratados.
3. Para cada tema aplica esta prueba en dos niveles:
   - **Encaje directo:** el tema coincide con el «Tema tratado» del subapartado. Se vincula.
   - **Encaje por definición:** el tema no está en «Tema tratado», pero entra claramente en la «Definición» (p. ej. una refinanciación frente a «autorización de transacciones materiales» de F.3.1). Se vincula y se marca **[A validar]**.
   - **Sin encaje:** el tema no cumple ninguno de los dos niveles. No se vincula.
4. **Confirma con la definición** cada vinculación directa: el tema debe afectar a lo que la definición describe (responsables, estructura, riesgos, controles, información financiera, supervisión). Si no lo hace, no la vincules.
5. **Agrupa por subapartado:** una sola línea por subapartado aunque haya varios temas, con los temas separados por «;».
6. **Redacta cada línea** con este formato exacto:
   `F.x.x: [tema o temas del acta] (Punto [n]). [Por qué encaja, con el concepto de la definición, en pocas palabras].`
   - La referencia al punto es obligatoria. Si el tema viene de «Otros asuntos» sin punto concreto, usa «(Otros asuntos)». Si el documento agrupa varias sesiones, añade la fecha: «(Punto 4, sesión 27/01/2027)».
   - Ordena las líneas de F.1.1 a F.5.2, con un salto de línea entre ellas.
7. **Casos especiales:**
   - Ningún tema encaja: `Sin vinculación con el apartado F.`
   - Acta parcial o solo con orden del día: añade al final `Nota: referencia basada en [el orden del día | un acta parcial]; solicitar acta completa.`
   - La vinculación depende de una celda del cliente que indica que falta información: márcala [A validar].
   - El tema encaja en dos subapartados: vincula ambos si las dos definiciones se cumplen; si dudas, elige el más probable y márcalo [A validar].

## Output
La columna L de cada fila, con una línea por subapartado. Ejemplo con un acta real del encargo, el **Consejo de Administración de 27/05/2025**:
```
F.1.1: Reorganización del Consejo de Administración de PRISA Media (Punto 3); nuevo CFO de la Compañía (Punto 4). Cambio en los órganos y la Alta Dirección responsables de la implantación y supervisión del SCIIF.
F.1.2: Reorganización de PRISA Media y salida de directivos (Punto 2). Cambio en la estructura organizativa y en las líneas de responsabilidad.
F.3.1: Aprobación de la actualización del Presupuesto 2025 e indicadores financieros a abril 2025 (Punto 4). Revisión por el Consejo de la información financiera y de las proyecciones relevantes.
```
En esa acta no se vinculan la aprobación del acta anterior (Punto 1) ni la delegación de facultades (Punto 7).

Otros ejemplos de línea:
```
F.1.1: Dª X asume la presidencia de la Comisión de Auditoría (Punto 6). Cambio en el órgano que supervisa la eficacia del SCIIF.
F.1.2: Actualización de la Política Anticorrupción (Punto 6). Código de conducta y su órgano de aprobación.
F.2.1: Venta de la filial Y (Punto 5). Cambio significativo en el alcance del grupo que obliga a actualizar la evaluación de riesgos. [A validar]
F.3.1: Aprobación de la refinanciación de la deuda (Punto 3). Autorización de una transacción material con impacto en la información financiera. [A validar]
F.5.1: Aprobación del Plan de Auditoría Interna (Punto 2). Plan anual de supervisión del SCIIF orientado a los riesgos de la información financiera.
F.5.2: Avance de la auditoría por el auditor externo (Punto 4, sesión 27/01/2027). Comunicación del auditor externo a la Comisión de Auditoría.
```

Para el cierre: nº de actas con vinculación, nº de vinculaciones por subapartado y nº de líneas [A validar].

## Rules
- Usa solo subapartados del Crossref y solo con un tema concreto del acta que lo sustente.
- Ejecutivo: cada línea ocupa como máximo dos renglones de la celda; sin adjetivos ni valoraciones; no copies el texto del Crossref.
- Nombres, cargos, ejercicios e importes tal como figuran en el acta o en la fila; el ejercicio se toma del acta, nunca de los ejemplos.
- No modifiques las columnas B a K.
- No valores si el acta basta para evidenciar el apartado F ni concluyas sobre la eficacia del SCIIF.

## Flow position
- Flow: Mapeo de actas
- Previous: `mapeo-resumen-actas` – receives: la matriz con B a K cumplimentadas y su trazabilidad interna
- Next: `mapeo-control-calidad-actas` – hands over: la matriz con la columna L cumplimentada y el recuento de vinculaciones
- Advance when: todas las filas tienen la columna L cumplimentada

## Failure handling
- La matriz no tiene B a K cumplimentadas: indica que hay que ejecutar antes `mapeo-resumen-actas`.
- Falta el Crossref: detente y avisa.
- Una fila solo tiene «No consta en el acta» en G, H e I: escribe `Sin vinculación con el apartado F.` y añade la nota de solicitar el acta completa.

## Final validation
- Todas las filas tienen la columna L cumplimentada.
- Cada línea empieza por un subapartado del Crossref, cita el punto o «Otros asuntos» y tiene justificación.
- No hay dos líneas del mismo subapartado en una fila.
- Toda vinculación por definición lleva [A validar].
