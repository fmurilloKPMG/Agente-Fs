---
name: mapeo-control-previo-actas
description: "Use this skill when the user starts Mapeo de actas, or re-attaches files in it, and the documentation must be checked before anything else: «Mapeo de actas», «resumen de actas», «mapeo de actas», «control de actas», actas del Consejo, Junta o Comisión de Auditoría, Excel «Análisis y resumen de actas». It checks that the files are enough and readable, identifies the entity and closing date, inventories the actas and returns CONTROL PREVIO ACTAS SUPERADO or NO SUPERADO explained in plain words. Do not use it to cross the control de actas, summarise actas, link them to apartado F or build the Excel (later Mapeo de actas skills), nor for Fichas técnicas."
metadata:
  version: "2.2"
---

# Control previo de actas · Mapeo de actas

Comprueba, antes de cumplimentar nada, que la documentación permite hacer el mapeo de actas, deja claro con qué se va a trabajar y explica al usuario, con palabras sencillas, qué está bien, qué falta y qué tiene que hacer. Solo revisa y clasifica: no extrae contenido de las actas.

## Resources
| Resource | Type | Location | Read / use at | If missing |
|---|---|---|---|---|
| `Plantilla Actas_Fs AGENTE IA.xlsx` | Template | Agent knowledge | Punto 1 | Control no superado: no se puede emitir el entregable |

## Workflow

### 1. Qué ha llegado
Comprueba que `Plantilla Actas_Fs AGENTE IA.xlsx` está en el conocimiento del agente y conserva sus cabeceras en la fila 23, columnas B a L. Después clasifica cada archivo adjunto:

| Tipo | Cómo se reconoce |
|---|---|
| Control de actas | Listado de la entidad con las reuniones del ejercicio por órgano (órgano y fecha, como mínimo). |
| Excel del cliente | «Análisis y resumen de actas», con columnas de acta, fecha, asistentes, puntos del día, acuerdos, otros asuntos, firmantes e impacto en EEFF, **sin** columna «Órgano». |
| Acta | Un documento con una sola acta, firmada o en borrador. |
| Varias actas en un documento | P. ej. las de la Comisión de Auditoría de varias sesiones. |
| Matriz aportada por el usuario | Una copia de la plantilla con datos; su información se traslada a la plantilla del conocimiento. |
| Otros | Presentaciones, anexos, una ficha técnica. No se procesan. |

### 2. Datos de la revisión
- Entidad y fecha de cierre o de análisis: las indica el usuario o, si no, la cabecera del Excel del cliente.
- ID del procedimiento: el del Excel del cliente, si existe.

### 3. Con qué se va a trabajar
- **Solo actas:** el resumen se elabora leyendo cada acta.
- **Excel del cliente o matriz del usuario:** se copia literal en la plantilla. Si además hay actas, se usan solo para contrastar.

### 4. Inventario de actas recibidas
Para cada acta (archivo, sesión o fila del Excel): título «Acta del [órgano] de dd/mm/aaaa», archivo o fila de origen, órgano normalizado (Consejo de Administración, Junta General de Accionistas, Comisión de Auditoría y Cumplimiento o su denominación literal, Comisión de Nombramientos y Retribuciones, Comité de Riesgos, Comité Ejecutivo / Comisión Delegada, Comisión de Sostenibilidad, órgano de administración de una filial con su nombre, u otro), fecha, si está firmada o es borrador, y si es parcial.

### 5. Situaciones que impiden continuar (NO SUPERADO)
Explica cada una así: qué pasa · por qué impide seguir · qué hacer.

| Situación | Cómo explicarla |
|---|---|
| No hay actas, ni Excel del cliente, ni matriz | «No he recibido ninguna acta ni el resumen del cliente, así que no tengo contenido que resumir. Adjunta las actas o el Excel “Análisis y resumen de actas”.» |
| Falta la plantilla en el conocimiento o está alterada | «No encuentro la plantilla “Plantilla Actas_Fs AGENTE IA” en el conocimiento del agente (o su estructura ha cambiado). Hay que cargarla para emitir el resultado.» |
| Un archivo no se puede leer | «No puedo leer [archivo] (protegido, dañado o escaneado sin texto). Vuelve a adjuntarlo sin contraseña o en PDF con texto seleccionable.» |
| No se conoce la entidad o la fecha de cierre | «Indícame la entidad y la fecha de cierre: las necesito para la cabecera y para saber hasta qué fecha comprobar las actas.» |
| El Excel del cliente no tiene la estructura esperada y no hay actas | «El Excel del cliente no tiene las columnas habituales (falta [columna]), así que no puedo trasladarlo con garantías. Revisa el archivo o adjunta las actas.» |

### 6. Situaciones que no impiden continuar (se avisan y se sigue)
| Situación | Cómo explicarla |
|---|---|
| Falta el control de actas | «No tengo el control de actas, así que no podré confirmar que están todas las reuniones. Sigo con las actas recibidas y lo dejo como pendiente de pedir al cliente.» |
| Acta sin fecha identificable | «En [acta] no identifico la fecha de la reunión. La resumo igualmente y lo señalo para confirmarlo.» |
| Posibles duplicados | «[A] y [B] parecen la misma reunión. Las mantengo y lo señalo para que lo revises.» |
| Actas en borrador | «[n] actas están sin firmar. Se pueden usar si la reunión es reciente, pero habrá que pedir la versión firmada.» |
| Acta parcial o solo orden del día | «[Acta] solo trae el orden del día o es parcial; el resumen será limitado y conviene pedir el acta completa.» |
| Excel del cliente desordenado o con varias actas en una fila | «Lo respeto tal cual, porque la información del cliente no se modifica, y lo señalo.» |
| Documentos que no se procesan | «Los archivos [lista] no son actas, así que no los proceso.» |

## Output
Mensaje al usuario:
```
CONTROL PREVIO ACTAS: SUPERADO | NO SUPERADO

Qué he recibido: [n] actas ([desglose por órgano]), control de actas [sí/no], Excel del cliente [sí/no].
Entidad: […] · Fecha de cierre: […]
Cómo voy a trabajar: [a partir de las actas | copiando el Excel del cliente (y contrastando con las actas)].

Lo que está bien: [una o dos líneas].
Lo que debes saber: [una línea por aviso] | Nada que destacar.
```
Si es NO SUPERADO, sustituye las dos últimas líneas por «Por qué no puedo continuar:» y «Qué necesito para seguir:», con una línea por punto.

Inventario para las siguientes skills: `Nº | Título | Archivo / fila origen | Órgano | Fecha | Firmada / Borrador | Parcial`.

## Rules
- No modifiques ningún archivo ni extraigas acuerdos, asistentes o contenido.
- No uses códigos internos en los mensajes.
- Solo las situaciones de la tabla del punto 5 dan NO SUPERADO.

## Flow position
- Flow: Mapeo de actas
- Previous: none – receives: los archivos adjuntos y los datos indicados por el usuario
- Next: `mapeo-completitud-actas` – hands over: resultado del control, entidad, fecha de cierre, modo de trabajo, avisos e inventario de actas
- Advance when: el resultado es CONTROL PREVIO ACTAS: SUPERADO

## Failure handling
- Duda sobre si un documento es un acta: inclúyelo en el inventario y avísalo.
- El usuario vuelve a adjuntar o sustituye archivos: repite el control completo.

## Final validation
- El mensaje empieza por una de las dos cabeceras de control.
- Cada NO SUPERADO corresponde a una situación del punto 5.
- El inventario incluye todas las actas recibidas con órgano y fecha.
