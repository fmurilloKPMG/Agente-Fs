---
name: preparacion-control-previo-informe
description: "Use this skill when the user starts Preparación de fichas, or re-attaches files in it: the standard report (Informe Anual de Gobierno Corporativo, IAGC, or its apartado F section) must be checked before building the fichas from the knowledge template («Preparación de fichas», «rellenar fichas desde el informe», «volcar el IAGC», «preparar fichas», «cumplimentar plantillas del apartado F»). It checks that the report text is readable and that the template Plantilla_Ficha_F_SCIIF.xlsx is in the agent knowledge, lists the subapartados F found with their internal titles, and returns CONTROL PREVIO INFORME SUPERADO or NO SUPERADO explained in plain words. Do not use it to extract or write the text into the fichas (preparacion-cumplimentacion-fichas), to review fichas against support documents (Fichas técnicas), nor for actas (Mapeo de actas)."
metadata:
  version: "1.2"
---

# Control previo del informe · Preparación de fichas

Comprueba, antes de cumplimentar nada, que el informe estándar se puede leer como texto y que la plantilla de ficha está en el conocimiento del agente, y lista los subapartados del apartado F que se van a preparar. Solo revisa y clasifica: no extrae párrafos ni modifica archivos.

## Resources
| Resource | Type | Location | Read / use at | If missing |
|---|---|---|---|---|
| `Plantilla_Ficha_F_SCIIF.xlsx` | Template | Agent knowledge | Punto 1 | Control no superado: las fichas se construyen siempre desde esta plantilla |

## Workflow

### 1. Qué ha llegado
Comprueba que `Plantilla_Ficha_F_SCIIF.xlsx` está en el conocimiento del agente y que conserva la hoja «F.x.x», la tabla `Table1` con sus tres columnas (Descripción efectuada por la Sociedad, Referencia a la documentación soporte y Comentario), la celda del título del apartado (B8) y el recuadro azul del código y la pregunta (B10). Después clasifica cada archivo adjunto:

| Tipo | Cómo se reconoce |
|---|---|
| Informe estándar | PDF o Word con el apartado «F. Sistemas internos de control y gestión de riesgos en relación con el proceso de emisión de la información financiera (SCIIF)» y los subapartados F.1.1, F.1.2… con su texto de respuesta. |
| Ficha técnica adjunta | Excel con una hoja «F.x.x» o con código F y una tabla `Table1`. No se usa como plantilla: las fichas se construyen siempre desde `Plantilla_Ficha_F_SCIIF.xlsx`. Se avisa al usuario. |
| Otros | Cualquier otro archivo. No se procesa. |

### 2. Legibilidad del informe (obligatoria)
1. Extrae el texto del informe completo.
2. Comprueba que el texto es legible: deben aparecer palabras corrientes en español («de», «la», «información», «financiera») y los códigos «F.1.1», «F.1.2»… escritos tal cual.
3. Si el texto sale como símbolos, letras sin sentido o vacío, el PDF tiene fuentes sin mapa de caracteres o es una imagen: el resultado es NO SUPERADO. No intentes adivinar ni reconstruir el texto.

### 3. Subapartados del informe
Lista los subapartados con respuesta, en orden: F.1.1, F.1.2, F.2.1, F.3.1, F.3.2, F.3.3, F.4.1, F.4.2, F.5.1, F.5.2, F.6 y F.7.1, o los que contenga el informe. Para cada uno indica cuántos títulos internos tiene. Un título interno es cada punto marcado con «·» bajo la pregunta del subapartado (p. ej. F.1.2 tiene cuatro: departamentos y mecanismos, código de conducta, canal de denuncias y programas de formación). Si no tiene ninguno, indica «sin títulos».

### 4. Fichas a preparar
- Cada subapartado es una ficha distinta: F.1.1 va en su ficha y F.1.2 en otra.
- Por defecto se preparan todos los subapartados del informe; si el usuario pide solo algunos, se preparan esos.
- Para cada uno anota el apartado al que pertenece y su título tal como aparece en el informe (p. ej. «F.1» y «Entorno de control de la entidad.»).
- Ejercicio: el que indique el usuario o, si no, el que figure en el informe.

### 5. Situaciones que impiden continuar (NO SUPERADO)
Explica cada una así: qué pasa · por qué impide seguir · qué hacer.

| Situación | Cómo explicarla |
|---|---|
| No hay informe | «No he recibido el informe estándar, así que no tengo texto que volcar. Adjunta el IAGC o su apartado F.» |
| El informe no tiene texto legible | «El PDF no tiene texto legible: al extraerlo salen símbolos en lugar de letras, porque sus fuentes no llevan mapa de caracteres o es una imagen. Si lo relleno así, el texto no sería fiel. Adjunta el Word original o un PDF con texto seleccionable, por ejemplo el descargado de la web de la CNMV.» |
| Falta la plantilla en el conocimiento o está alterada | «No encuentro la plantilla “Plantilla_Ficha_F_SCIIF” en el conocimiento del agente (o le falta la tabla Table1 o el recuadro del título). Hay que cargarla para preparar las fichas.» |
| El informe no contiene subapartados F | «El documento no incluye el apartado F con sus subapartados (F.1.1, F.1.2…). Revisa que es el informe correcto.» |

### 6. Situaciones que no impiden continuar (se avisan y se sigue)
| Situación | Cómo explicarla |
|---|---|
| Se han adjuntado fichas | «He recibido fichas adjuntas, pero las fichas se construyen siempre desde la plantilla limpia del conocimiento; no uso las adjuntas.» |
| Subapartado sin texto de respuesta | «El subapartado [código] aparece en el informe sin texto de respuesta; su ficha quedará con la parte amarilla vacía.» |

## Output
```
CONTROL PREVIO INFORME: SUPERADO | NO SUPERADO

Informe: [archivo] · Entidad: [según el informe] · Ejercicio: [ejercicio]
Texto del informe: legible | no legible

Plantilla: Plantilla_Ficha_F_SCIIF.xlsx (conocimiento del agente)

Fichas a preparar:
| Subapartado | Apartado y título | Títulos internos |

Lo que debes saber: [una línea por aviso] | Nada que destacar.
```
Si es NO SUPERADO, sustituye la tabla y los avisos por «Por qué no puedo continuar:» y «Qué necesito para seguir:», con una línea por punto.

## Rules
- No modifiques ningún archivo ni extraigas párrafos.
- No uses códigos internos en los mensajes.
- Solo las situaciones del punto 5 dan NO SUPERADO.

## Flow position
- Flow: Preparación de fichas
- Previous: none – receives: el informe estándar y el ejercicio indicado por el usuario
- Next: `preparacion-cumplimentacion-fichas` – hands over: resultado del control, entidad, ejercicio, tabla de fichas a preparar (subapartado, apartado y título, nº de títulos) y avisos
- Advance when: el resultado es CONTROL PREVIO INFORME: SUPERADO

## Failure handling
- El informe no se puede abrir: NO SUPERADO.
- El usuario vuelve a adjuntar o sustituye archivos: repite el control completo.

## Final validation
- El mensaje empieza por una de las dos cabeceras de control.
- Se ha comprobado la legibilidad del texto, no solo que el archivo abre.
- La plantilla del conocimiento existe y conserva su estructura.
- Cada ficha a preparar corresponde a un único subapartado.
