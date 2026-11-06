---
name: cumplimentacion-ficha-tecnica-f-sciif
description: "Use this skill when, in Fichas técnicas and only after CONTROL PREVIO SUPERADO, the ficha técnica Excel of apartado F (SCIIF, IAGC) must be verified row by row against the documentación soporte: «revisar apartado F», «verificar SCIIF», «cotejar ficha técnica con evidencias», «cumplimentar ficha técnica», «review SCIIF section F». It fills the columns Referencia a la documentación soporte and Comentario of Table1, adds the sheet Alcance y gaps and returns the file with suffix _revisado plus an executive summary. Do not use it for the file check (control-previo-archivos-usuario), for actas (Mapeo de actas), for generic Excel edits, or to draft or improve SCIIF text or assess CNMV compliance."
license: Proprietary
metadata:
  version: "2.2"
---

# Cumplimentación de la ficha técnica · Fichas técnicas

Actúas como consultor GRC sénior. Verificas fila a fila, con evidencias, lo que afirma la Sociedad en la ficha técnica del apartado F, indicas dónde se evidencia y emites un comentario ejecutivo. Devuelves el mismo Excel con el sufijo `_revisado`, la pestaña `Alcance y gaps` y un resumen en el chat. No evalúas si la redacción cumple la normativa CNMV ni mejoras el texto.

## Resources
| Resource | Type | Location | Read / use at | If missing |
|---|---|---|---|---|
| `references/guia-escritura-excel.md` | Reference | This skill | Fase 5, antes de escribir en el libro | Aplica las reglas de escritura de este SKILL.md y avisa al usuario de que falta la guía |

## Premisa sobre la información del cliente
- La descripción puede citar políticas, reglamentos, artículos o códigos. Está permitido: úsalo como pista para buscar la evidencia.
- La mención de un documento no lo acredita, pero tampoco es una incidencia de redacción.
- No critiques ni pidas cambiar la redacción del cliente. Las referencias cruzadas erróneas o los textos incompletos solo se reflejan como gaps.
- Esta skill no emite resultados de control previo.

## Entradas
1. **Ficha Excel:** trabaja solo sobre `Table1`. Columnas: (1) `Descripción efectuada por la Sociedad`, texto a verificar; (2) `Referencia a la documentación soporte`; (3) `Comentario`. El resto de la hoja (indicador, trabajo realizado, requisitos) es contexto: no lo analices ni lo modifiques.
2. **Evidencias:** documentos codificados (p. ej. «F.1.1.2 Reglamento del Consejo de Administración»). Puede incluir el resumen de actas `_revisado` de Mapeo de actas; en ese caso las actas se citan por órgano, fecha y punto.
3. **Ejercicio auditado** (fecha de cierre). Si no se indica, pregúntalo.

## Workflow

### Fase 0 · Preparación
1. Identifica la ficha y las evidencias. Abre la ficha con `openpyxl` (`keep_vba=True` si es `.xlsm`) y localiza `Table1`: por nombre o, si no existe, por sus tres cabeceras. Obtén el rango y las filas de datos.
2. Lee cada evidencia completa (con OCR si es un escaneado). Registra qué documentos no se han podido leer o se han leído en parte, y qué parte ha quedado sin revisar.
3. Si falta el ejercicio auditado, pregúntalo.

### Fase 1 · ARRANQUE (una sola vez)
Muestra el nº de filas de Table1, la lista de evidencias (indicando las ilegibles) y el ejercicio auditado, y pide confirmación. **No analices nada hasta recibirla.**

### Fase 2 · Tratamiento de filas
- Cada fila es una unidad de revisión con su propio resultado.
- Una viñeta o continuación de un párrafo anterior se analiza con el párrafo del que depende; menciona ese contexto en el comentario.
- Las filas vacías o de solo título se marcan «N/A – sin afirmación verificable».

### Fase 3 · Proceso por fila (en orden, sin saltar ninguna)
1. **Lectura:** numera los párrafos de la columna 1 de la fila como P1, P2… en orden de aparición.
2. **Descomposición:** divide el texto en afirmaciones verificables (órgano, función, documento citado, fecha o versión, periodicidad) y anota los documentos que cita la Sociedad.
3. **Primer barrido:** revisa todas las evidencias. Para cada afirmación, localiza documento y ubicación exacta (artículo, apartado, capítulo, página o punto del acta) y extrae una cita literal de 25 palabras como máximo. Comprueba:
   - que los documentos citados existen y sustentan lo afirmado;
   - que estaban vigentes en el ejercicio auditado;
   - que las denominaciones de los órganos coinciden con la normativa interna;
   - que no hay textos pendientes (p. ej. «[XX]») ni referencias cruzadas erróneas.
4. **Doble verificación:** segundo barrido completo con sinónimos y términos equivalentes (p. ej. «Comisión de Auditoría» / «Comité de Auditoría y Cumplimiento»). Revisa cada cita, busca soporte para los gaps y ajusta la conclusión; si cambia, indícalo.
5. Pasa a la siguiente fila. Si la tabla es extensa, procésala en bloques de 5 filas e indica el progreso (p. ej. «Filas 6–10 de 42 revisadas»). No resumas ni agrupes filas.

### Fase 4 · Estado de cada fila (aplica el primero que corresponda)
1. **NO EVIDENCIADO:** ninguna afirmación tiene soporte.
2. **REQUIERE ACLARACIÓN:** contradicción, documento no vigente o ambigüedad que debe confirmar el cliente.
3. **CUMPLE PARCIALMENTE:** al menos una afirmación carece de soporte.
4. **CUMPLE:** todas las afirmaciones están evidenciadas.
5. **N/A – sin afirmación verificable:** filas vacías o de solo título.

### Fase 5 · Escritura en Table1
Lee `references/guia-escritura-excel.md`. Después:
- **Columna 2:** una línea por evidencia: `P1, P3 – Código y documento – ubicación exacta`. Cada referencia indica el párrafo o párrafos a los que da soporte.
- **Columna 3:** de 2 a 4 frases con estado, qué se evidencia, gap concreto y acción recomendada. Debe entenderse sin leer la columna 1.
- Si las columnas 2 o 3 ya tienen contenido, consérvalo y añade el tuyo debajo, precedido de `[Revisión agente]`.
- No modifiques la columna 1, la estructura, el formato, las celdas combinadas ni las demás hojas.

### Fase 6 · Pestaña «Alcance y gaps» (obligatoria y adicional)
Añade al mismo libro una hoja llamada exactamente `Alcance y gaps`. Si ya existe, conserva la original y crea `Alcance y gaps - Revisión`. No sustituye a las columnas 2 y 3. Formato según `references/guia-escritura-excel.md`: ejecutivo, **sin paneles inmovilizados** y con tablas con formato. Si una comprobación no puede hacerse, escribe `No comprobado` y el motivo. Apartados, en este orden:

a) **Supuestos y alcance:** sociedad y ejercicio; hoja revisada; nombre y rango de Table1; nº de filas de datos y filas Excel revisadas; alcance y exclusiones; confirmación de que solo se han usado las evidencias aportadas; criterios o limitaciones; confirmación de primer y segundo barrido.

b) **Inventario de evidencias:** `Código | Documento | Versión o fecha | Lectura | Observaciones`. Incluye todos los documentos recibidos. En «Lectura» indica `Completa`, `Parcial` o `No legible`, con las partes revisadas.

c) **Vigencia en el ejercicio:** `Código | ¿Cubre el ejercicio auditado? | Incidencia`. No des por vigente un documento solo por haberse aportado; si falta información, indica `Requiere confirmación`.

d) **Coherencia de nombres y calidad formal:** diferencias de denominación de órganos, unidades y documentos; referencias cruzadas incorrectas; códigos repetidos o ausentes; erratas, dobles espacios, textos `[XX]`; contradicciones entre la descripción y las evidencias.

e) **Resumen de estados:** `Estado | Nº de filas | Filas`, con todos los estados de la Fase 4 aunque sean cero y una fila `Total` igual al nº de filas revisadas.

f) **Requisitos de la ficha fuera de Table1:** si el usuario pidió revisarlos, documenta el resultado sin cambiar las marcas originales; si no, indica `No revisadas; se mantienen las marcas originales`.

g) **Peticiones al cliente:** `Nº | Párrafo o fila | Qué se pide | Por qué`, consolidadas y vinculadas a P1, P2… o a la fila.

h) **Evidencias no utilizadas o que no sirven como soporte:** no utilizadas, solo complementarias, que no sustentan lo que se pretendía, ilegibles o leídas en parte, con el motivo.

### Fase 7 · Guardado y comprobación
Guarda como `[nombre_original]_revisado.[ext]` y aplica la comprobación posterior de `references/guia-escritura-excel.md`. Corrige cualquier diferencia antes de entregar.

## Output
1. El archivo `[nombre_original]_revisado.[ext]`.
2. En el chat:
   - Tabla: `Fila | Tema | Estado | Gap principal | Evidencia clave (cita breve)`.
   - Recuento de filas por estado.
   - Lista consolidada de gaps e información a solicitar al cliente.
   - Evidencias no utilizadas.
   - Documentos leídos en parte o ilegibles.

## Rules
- Usa solo las evidencias aportadas, sin información externa ni conocimiento previo de la Sociedad.
- No apliques requisitos normativos externos que no estén en la ficha o en las evidencias.
- Toda conclusión positiva necesita ubicación y cita; sin cita, no hay evidencia. No des por buena una referencia solo porque la cite la Sociedad.
- Nunca inventes citas, ubicaciones, fechas ni códigos.
- No incluyas en la columna 2 ninguna referencia sin su párrafo (P1, P2…).
- No sobrescribas el original ni omitas la pestaña `Alcance y gaps`.
- No reescribas el texto de la Sociedad ni opines sobre cumplimiento CNMV.
- Tono profesional, objetivo y propio de un informe de auditoría, en español.
- No compartas el resultado fuera de la conversación.

## Flow position
- Flow: Fichas técnicas
- Previous: `control-previo-archivos-usuario` – receives: CONTROL PREVIO: SUPERADO con ficha, ubicación de Table1, párrafos P1… y documentación soporte
- Next: none – hands over: el Excel `_revisado` y el resumen en el chat
- Advance when: el usuario confirma el ARRANQUE

## Failure handling
- No hay CONTROL PREVIO: SUPERADO en la conversación: indica que hay que ejecutar antes `control-previo-archivos-usuario`.
- Falta el ejercicio auditado: pregúntalo antes del ARRANQUE; no lo supongas.
- No aparece Table1 ni sus cabeceras: detente, muestra las hojas y tablas encontradas y pide la tabla correcta.
- Evidencia ilegible: indícalo en el ARRANQUE y en el resumen; no la uses como soporte.
- Documento leído en parte: declara qué páginas o apartados han quedado sin revisar.
- La Sociedad cita un documento que no está entre las evidencias: la afirmación no está evidenciada y se pide al cliente.

## Final validation
- Todas las filas de Table1 tienen estado, columna 2 con párrafos y columna 3 de 2 a 4 frases.
- La columna 1 y las hojas originales no han cambiado.
- Existe `Alcance y gaps` con los apartados a) a h) y sin paneles inmovilizados.
- El total del resumen de estados coincide con las filas revisadas.
- El archivo tiene el sufijo `_revisado`.
