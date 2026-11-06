# Guía técnica · Escritura de la parte amarilla de Table1

Se lee en la fase 4 de `preparacion-cumplimentacion-fichas`, antes de escribir en la ficha.

## Principios
- Se edita la plantilla original con `openpyxl` (`keep_vba=True` si es `.xlsm`). Nunca se recrea el libro ni se exporta desde pandas.
- Se parte siempre de una copia de `Plantilla_Ficha_F_SCIIF.xlsx`.
- Solo se escribe en el nombre de la hoja, B8, B10, el código de B21, la columna A (títulos) y la primera columna de `Table1` (párrafos, parte amarilla) de las filas de datos de la tabla.
- Todo lo que está debajo de la tabla (lectura y entendimiento, requisitos, trabajo realizado, comentarios) se conserva tal cual. Si hacen falta filas nuevas, se desplaza hacia abajo, nunca se sobrescribe.

## Fragmento de referencia
```python
import copy, math
from openpyxl import load_workbook
from openpyxl.worksheet.cell_range import CellRange

wb = load_workbook(ruta_plantilla)                  # copia de Plantilla_Ficha_F_SCIIF.xlsx
ws = wb["F.x.x"]
ws.title = codigo                                   # p. ej. "F.1.1"
ws["B8"].value = f"{codigo_apartado} {titulo_apartado}"   # p. ej. "F.1 Entorno de control de la entidad."
ws["B10"].value = f"{codigo} {pregunta}"                  # recuadro azul
if isinstance(ws["B21"].value, str):
    ws["B21"].value = ws["B21"].value.replace("F.x.x", codigo)
tabla = ws.tables["Table1"]
rango = CellRange(tabla.ref)
primera = rango.min_row + 1                         # primera fila de datos
ultima = rango.max_row
col_desc = rango.min_col                            # columna amarilla (B)
bloques = [...]                                     # lista de dicts {"titulo": str, "texto": str}

faltan = len(bloques) - (ultima - primera + 1)
if max(faltan, 0):
    insertar_en = ultima + 1
    alturas = {r: ws.row_dimensions[r].height for r in range(insertar_en, ws.max_row + 1)}
    combinadas = [m for m in ws.merged_cells.ranges if m.min_row in range(insertar_en, ws.max_row + 2)]
    for m in combinadas:
        ws.unmerge_cells(str(m))
    ws.insert_rows(insertar_en, faltan)
    for m in combinadas:
        m.shift(0, faltan)
        ws.merge_cells(str(m))
    for r in range(ws.max_row, insertar_en - 1, -1):
        ws.row_dimensions[r].height = None
    for r, h in alturas.items():
        ws.row_dimensions[r + faltan].height = h
    for r in range(insertar_en, insertar_en + faltan):   # copia el formato de la última fila de datos
        for c in range(1, rango.max_col + 2):
            ws.cell(r, c)._style = copy.copy(ws.cell(ultima, c)._style)
    ultima += faltan

rango.expand(down=ultima - rango.max_row)
tabla.ref = rango.coord
if tabla.autoFilter is not None:
    tabla.autoFilter.ref = rango.coord

caracteres_linea = (ws.column_dimensions["B"].width or 110) * 1.25
for i, fila in enumerate(range(primera, ultima + 1)):
    bloque = bloques[i] if i in range(len(bloques)) else {"titulo": None, "texto": None}
    ws.cell(fila, 1).value = bloque["titulo"] or None
    ws.cell(fila, col_desc).value = bloque["texto"]
    ws.cell(fila, col_desc + 1).value = None           # Referencia a la documentación soporte
    ws.cell(fila, col_desc + 2).value = None           # Comentario
    alin = copy.copy(ws.cell(fila, 1).alignment)
    alin.wrap_text = True
    alin.vertical = "top"
    ws.cell(fila, 1).alignment = alin
    if bloque["texto"]:
        lineas = sum(max(1, math.ceil(len(p) / caracteres_linea)) for p in bloque["texto"].split("\n"))
        ws.row_dimensions[fila].height = min(409, lineas * 12 + 6)
wb.save(ruta_salida)
```

## Comprobación posterior
1. Reabre el archivo guardado.
2. Para cada fila de datos, el texto de la columna amarilla debe ser idéntico, carácter a carácter, al texto extraído del informe, y la columna A igual al título.
3. Comprueba que `Table1` abarca la cabecera y todas las filas de datos, y que su autofiltro tiene el mismo rango.
4. Compara el resto de celdas con la plantilla: deben tener el mismo valor, solo desplazadas el número de filas insertadas.
5. Los rangos combinados de debajo de la tabla deben conservarse, desplazados.
6. No debe haber paneles inmovilizados (`ws.freeze_panes` vacío) ni hojas nuevas.

## Límite de Excel
La altura máxima de una fila en Excel es de 409 puntos. Si el texto no cabe en esa altura, sigue completo en la celda, pero en pantalla se ve recortado. Avisa al usuario de qué filas están en ese caso.
