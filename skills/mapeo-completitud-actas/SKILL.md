---
name: mapeo-completitud-actas
description: "Use this skill when, inside Mapeo de actas and right after CONTROL PREVIO ACTAS SUPERADO, it must be checked that all actas of the year are available: crosses the control de actas with the actas received by órgano and fecha, up to the closing date, and marks each meeting as firmada, en borrador or no recibida («completitud de actas», «faltan actas», «cuadre con el control de actas»). It produces the completitud table by órgano and the grouped points for the review tab. Do not use it to summarise actas, link them to apartado F or build the Excel, nor for Fichas técnicas."
metadata:
  version: "2.2"
---

# Completitud de actas · Mapeo de actas

Confirma que tenemos el acta de todas las reuniones celebradas en el ejercicio hasta la fecha de análisis, órgano por órgano, y en qué estado está cada una.

## Workflow
1. **Reuniones que deberían existir:** toma del control de actas todas las reuniones hasta la fecha de análisis, con órgano y fecha, usando los mismos nombres de órgano que el inventario.
2. **Cruce órgano a órgano:** busca cada reunión entre las actas recibidas por órgano y fecha y asígnale un estado:
   - **Recibida y firmada.**
   - **Recibida en borrador:** válida si la reunión es reciente y el acta aún no está formalizada; hay que pedir la versión firmada.
   - **No recibida:** hay que pedirla al cliente.
3. **Actas que no figuran en el control:** se resumen igualmente y se señalan para que el cliente confirme si faltan en su listado.
4. **Coincidencias dudosas:** si coincide el órgano pero la fecha difiere en uno o dos días, o al revés, no la des por buena; señálala para revisarla.
5. **Si no hay control de actas:** no inventes las reuniones. Construye el cuadro solo con las actas recibidas, con la nota «No se puede confirmar que estén todas: falta el control de actas», y genera el pendiente de pedir el control de actas del ejercicio hasta la fecha de análisis.

## Output
1. **Cuadro de completitud por órgano**, una línea por órgano y una línea de total:
   `Órgano | Reuniones según el control | Actas firmadas | Actas en borrador | Actas que faltan | Situación`
   La «Situación» se redacta en una frase, por ejemplo: «Completo: las 12 reuniones tienen acta firmada.», «Faltan 2 actas (14/05 y 20/05) y 1 está en borrador (28/10).» o «No verificable: falta el control de actas; se han recibido 3 actas.»
2. **Puntos agrupados**, una línea por punto aunque afecte a varias actas:
   - «Faltan las actas de: Consejo de Administración 14/05 y 20/05; Comisión de Auditoría 18/03.»
   - «Actas en borrador, pedir versión firmada: Consejo de Administración 28/10.»
   - «Actas recibidas que no figuran en el control: Junta General 14/05.»
   - «Fechas que no cuadran con el control: Comisión de Auditoría 24/02 (control: 25/02).»

## Rules
- Usa solo el control de actas y las actas aportadas.
- Ordena por órgano y, dentro de cada órgano, por fecha.
- No concluyas sobre si la documentación es suficiente para el apartado F.

## Flow position
- Flow: Mapeo de actas
- Previous: `mapeo-control-previo-actas` – receives: inventario de actas, fecha de análisis, avisos y, si existe, el control de actas
- Next: `mapeo-resumen-actas` – hands over: inventario confirmado, cuadro de completitud y puntos agrupados
- Advance when: el cuadro de completitud está terminado

## Failure handling
- No hay CONTROL PREVIO ACTAS: SUPERADO en la conversación: indica que hay que ejecutar antes `mapeo-control-previo-actas`.
- El control de actas no se puede leer completo: trátalo como ausente y avísalo.

## Final validation
- Cada reunión del control tiene un estado.
- El total del cuadro cuadra con el detalle.
- Ningún punto aparece repetido en varias líneas.
