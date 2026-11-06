# Agente SCIIF · Apartado F (Copilot Studio, GitHub Copilot harness)

Versión 4.0 · 06/11/2026 · Generado con la skill `copilot-studio-agent-builder` (validación: 9 skills, 0 errores, 0 avisos).

## Dónde va cada archivo en Copilot Studio

| Archivo | Dónde se pone | Cómo |
|---|---|---|
| `agent/description.txt` | Agente → Descripción | Copiar y pegar |
| `agent/instructions.txt` | Agente → Instrucciones | Copiar y pegar tal cual (texto plano) |
| `agent/knowledge/Plantilla_Ficha_F_SCIIF.xlsx` | Agente → Conocimiento | Subir con **este nombre exacto** (nueva): plantilla limpia de ficha de Preparación de fichas |
| `agent/knowledge/Plantilla Actas_Fs AGENTE IA.xlsx` | Agente → Conocimiento | Subir con **este nombre exacto**; sustituye la versión anterior de la plantilla |
| `skills/preparacion-control-previo-informe.zip` | Build → Skills → Add skill → Upload a skill | Subir el .zip sin descomprimir (nueva) |
| `skills/preparacion-cumplimentacion-fichas.zip` | Ídem | Ídem (nueva; incluye la guía de inserción de filas en Table1) |
| `skills/control-previo-archivos-usuario.zip` | Build → Skills → Add skill → Upload a skill | Subir el .zip sin descomprimir |
| `skills/cumplimentacion-ficha-tecnica-f-sciif.zip` | Ídem | Ídem |
| `skills/mapeo-control-previo-actas.zip` | Ídem | Ídem |
| `skills/mapeo-completitud-actas.zip` | Ídem | Ídem |
| `skills/mapeo-resumen-actas.zip` | Ídem | Ídem |
| `skills/mapeo-cruce-actas-sciif.zip` | Ídem | Ídem (incluye el Crossref con definiciones) |
| `skills/mapeo-control-calidad-actas.zip` | Ídem | Ídem |

Las carpetas `skills/<nombre>/` son el código fuente editable de cada skill. No se suben.

Si ya tenías estas skills cargadas, **elimina primero la versión anterior** de cada una. Copilot Studio rechaza dos skills con el mismo nombre.

## Procesos (independientes, en cualquier orden)

- **Preparación de fichas:** `preparacion-control-previo-informe` → (CONTROL PREVIO INFORME: SUPERADO) → `preparacion-cumplimentacion-fichas` (con confirmación del usuario antes de escribir).
- **Fichas técnicas:** `control-previo-archivos-usuario` → (CONTROL PREVIO: SUPERADO) → `cumplimentacion-ficha-tecnica-f-sciif`.
- **Mapeo de actas:** `mapeo-control-previo-actas` → (CONTROL PREVIO ACTAS: SUPERADO) → `mapeo-completitud-actas` → `mapeo-resumen-actas` → `mapeo-cruce-actas-sciif` → `mapeo-control-calidad-actas`.

Las skills `paso0-*` y `paso2-*` de versiones anteriores se han renombrado: borra las antiguas en Copilot Studio antes de subir las nuevas.

## Configuración recomendada

- **Conocimiento:** `Plantilla_Ficha_F_SCIIF.xlsx` y `Plantilla Actas_Fs AGENTE IA.xlsx`. El Crossref va dentro de la skill de cruce, así que no hace falta subirlo al conocimiento.
- **Búsqueda web («Search all websites»):** desactivarla. El agente solo debe trabajar con los documentos aportados.
- **Herramientas o conectores:** ninguno.
- **Memoria:** desactivada, porque cada encargo es independiente.

## Mantenimiento del Crossref

Si cambia el Crossref:
1. Edita `skills/mapeo-cruce-actas-sciif/references/crossref-actas-apartado-f.md`.
2. Sube `metadata.version` en el SKILL.md.
3. Vuelve a empaquetar con `validate_and_pack.py` y sustituye la skill en Copilot Studio.

## Supuestos que hay que comprobar en Copilot Studio

- **Estructura del .zip.** Cada .zip lleva una carpeta con el nombre de la skill y su `SKILL.md` dentro. Si la subida falla, vuelve a empaquetar con `--flat`, que pone `SKILL.md` en la raíz.
- **Número de skills por agente.** El límite no está confirmado y circulan cifras de 8 y de 100. Este agente usa 9: si Copilot Studio no admite más de 8, fusionar `mapeo-completitud-actas` dentro de `mapeo-control-previo-actas`.
- **PDF del informe en Preparación de fichas.** Algunos PDF (p. ej. los re-generados con Acrobat Distiller y fuentes sin mapa de caracteres) no devuelven texto legible. La skill de control previo lo detecta y pide el Word o un PDF con texto seleccionable.
- **Altura de fila.** Excel limita la altura de fila a 409 puntos: los subapartados largos quedan completos en la celda pero recortados en pantalla.
- **Longitud de las instrucciones y de cada SKILL.md.** El límite tampoco está confirmado. Las instrucciones tienen unos 5.500 caracteres y ninguna skill pasa de 10.000.
- **Lectura del conocimiento.** Que el conocimiento se lea completo no está garantizado. Por eso las reglas obligatorias están en las skills y no en la plantilla.

## Scripts

Ninguna skill incluye scripts. El sandbox genera y edita el Excel de forma nativa.
