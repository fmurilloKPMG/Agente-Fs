# Guía técnica de escritura del Excel revisado

Se lee en la fase 5 de `cumplimentacion-ficha-tecnica-f-sciif`, antes de escribir en el libro.

## Carga y escritura
1. Edita el libro original. Nunca lo recrees desde cero ni lo exportes desde pandas.
2. Carga con `openpyxl.load_workbook(ruta_original)`, sin `data_only=True` para no perder fórmulas. Si es `.xlsm`, añade `keep_vba=True`.
3. Escribe **solo** en las columnas 2 y 3 de las filas de datos de Table1. No toques la columna 1, la cabecera, las columnas de contexto, las demás hojas, los anchos, los estilos ni las celdas combinadas: `ws.merged_cells` debe quedar igual.
4. Contenido de cada celda:
   - Si la celda está vacía, escribe tu texto directamente.
   - Si la celda ya tiene contenido, escribe `contenido_existente + "\n\n[Revisión agente]\n" + tu_texto`.
   - En la columna 2, separa cada evidencia con un salto de línea (`\n`).
5. Mantén el ajuste de texto: copia el objeto `Alignment` existente con `wrap_text=True`, sin cambiar fuente, bordes ni relleno. Si la celda forma parte de un rango combinado, escribe solo en la celda superior izquierda.

## Fragmento de referencia
```python
from copy import copy
from openpyxl import load_workbook
from openpyxl.utils import range_boundaries

wb = load_workbook(ruta_original)            # keep_vba=True si es .xlsm
ws, tabla = localizar_table1(wb)             # por nombre "Table1" o por cabeceras
min_col, min_row, max_col, max_row = range_boundaries(tabla.ref)
col2, col3 = min_col + 1, min_col + 2        # columnas 2 y 3 de Table1
for fila in range(min_row + 1, max_row + 1): # excluye la cabecera
    for col, texto in ((col2, ref_fila[fila]), (col3, comentario_fila[fila])):
        celda = ws.cell(row=fila, column=col)
        previo = celda.value
        celda.value = f"{previo}\n\n[Revisión agente]\n{texto}" if previo else texto
        alin = copy(celda.alignment); alin.wrap_text = True
        celda.alignment = alin
wb.save(ruta_revisado)                       # [nombre]_revisado.[ext]
```

## Hoja «Alcance y gaps»: formato
- Título en A1: `Alcance y gaps de la revisión`, en negrita y con un tamaño mayor.
- Columnas A:E con anchos aproximados de 28, 45, 30, 45 y 40.
- Encabezados de tabla en negrita, con relleno de color corporativo suave y bordes finos.
- Ajuste de texto y alineación superior en todas las celdas con contenido.
- Las filas de texto explicativo se combinan de A a E cuando proceda.
- **Sin paneles inmovilizados** (`ws.freeze_panes = None`) y sin filtros que oculten filas.
- Sin fórmulas con vínculos externos.
- Estilo ejecutivo: un bloque por apartado, con una fila en blanco entre bloques.

## Guardado y comprobación
1. Guarda como `[nombre_original]_revisado.[ext]`, con la misma extensión, y entrégalo al usuario como archivo descargable.
2. Reabre el fichero guardado y comprueba:
   - las hojas originales siguen ahí con sus nombres y la única hoja nueva es `Alcance y gaps`;
   - los rangos combinados y el rango de Table1 no han cambiado;
   - la columna 1 de Table1 es idéntica a la original;
   - solo hay contenido nuevo en las columnas 2 y 3 de Table1 y en la hoja nueva.
3. Si el libro contiene elementos que `openpyxl` puede no conservar (gráficos, imágenes, controles), avisa al usuario.
