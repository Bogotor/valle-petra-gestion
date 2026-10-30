# Pantalla: Dashboard

> Reglas específicas de esta pantalla. **Prevalecen** sobre `design-system/MASTER.md` solo en lo que se indica
> en la sección "Diferencias con MASTER"; en todo lo demás rige MASTER.

- **Estado:** aprobado (v1, 29-10-2026), con la regla de ingresos por fecha de llegada.
- **Módulos de SPEC:** §3.6 Dashboard mensual y §3.8 Exportar planilla del mes.
- **Para qué:** responder **"¿cómo va el mes?"** en un vistazo y **bajar la planilla Excel** para la
  contabilidad.
- **Idea central:** pocas cifras, grandes y explicadas en palabras. Cada número dice **qué cuenta** debajo
  ("113 de 186 noches", "1 reserva"). Nada de gráficos que haya que interpretar: barras simples con el valor
  siempre escrito.

---

## 1. Cómo se llega

- Barra inferior › **Dashboard** (pestaña activa en terracota + 700, MASTER).
- Siempre abre en el **mes actual**.

---

## 2. Estructura

```
┌──────────────────────────────────────┐
│ Dashboard                       (H1)  │
│ [‹]       Octubre 2026          [›]   │  1. Mes
│ Datos al 29-10 · Beds24 hace 2 min    │
│                                       │
│ ┌ Ingresos de octubre ──────────────┐ │  2. Cifras principales
│ │ $9.110.250                         │ │
│ │ Ya descontadas las comisiones de   │ │
│ │ Airbnb y Booking                   │ │
│ └────────────────────────────────────┘ │
│ ┌ Ocupación ──────┐ ┌ Por cobrar ────┐ │
│ │ 61%             │ │ $90.000        │ │
│ │ 113 de 186 noch.│ │ 1 reserva    › │ │
│ └─────────────────┘ └────────────────┘ │
│                                       │
│ Por canal                             │  3. Por canal
│ Airbnb   ███████████████  $3.145.500  │
│          43 noches                    │
│ Directo  ██████████████   $2.930.000  │
│          34 noches · sin comisión     │
│ Booking  ████████████     $2.644.750  │
│          36 noches                    │
│ Comisiones del mes: $1.024.750        │
│                                       │
│ Por cabaña                            │  4. Por cabaña
│ Domo Anko     ████████████   81%      │
│ Domo Llu Llu  ███████████    77%      │
│ …                                     │
│                                       │
│ Servicios                             │  5. Servicios
│ Tinajas    9 usos · $390.000          │
│ Quincho    6 usos · sin costo         │
│                                       │
│ Próximos 7 días                       │  6. Próximos 7 días
│ 6 llegadas · 8 salidas · 8 aseos      │
│                                       │
│ [⤓ Descargar planilla de octubre   ]  │  7. Exportar
│ Excel para la contabilidad. Sin       │
│ teléfonos ni emails.                  │
├──────────────────────────────────────┤
│   Inicio      ( + Nuevo )   Dashboard │  Barra inferior FIJA
└──────────────────────────────────────┘
```

---

## 3. Secciones

### 1. Mes
- "‹" y "›" de 48px a los lados del mes ("Octubre 2026", 20px/700). Se puede ir a meses pasados y a meses
  futuros.
- Mes futuro: debajo, "Reservas hechas hasta hoy" (todavía pueden llegar más).
- Línea de frescura (14px, apagada): "Datos al 29-10 · Beds24 hace 2 min". Si la última sincronización tiene
  más de 10 min: "Beds24 sin actualizar hace 25 min" en ámbar (los números de Airbnb/Booking pueden estar
  atrasados).

### 2. Cifras principales
Tres tarjetas. La de ingresos ocupa todo el ancho; ocupación y por cobrar van lado a lado.

| Cifra | Qué cuenta | Texto de apoyo |
|---|---|---|
| **Ingresos de octubre** | Estadías de las reservas que **llegan** en el mes, sin canceladas (las de Airbnb/Booking canceladas allá llegan por Beds24 y se restan solas), **menos** la comisión de Airbnb y Booking, **más** las tinajas del mes. | "Ya descontadas las comisiones de Airbnb y Booking" |
| **Ocupación** | Noches ocupadas del mes ÷ noches disponibles (unidades activas × días del mes: hoy 6 × 31 = 186 en octubre; una unidad inactiva, por ejemplo en renovación, no cuenta). | "113 de 186 noches" |
| **Por cobrar** | Lo que falta pagar (estadía + tinajas − pagos) de las reservas que **llegan** en el mes, sin canceladas. Misma regla que los ingresos. | "1 reserva" + ›: lleva a Registrar pago, que parte en la lista de reservas con saldo. Si es $0: "✓ Todo cobrado". |

- **Regla para contar ingresos: por fecha de llegada.** Una reserva del 31 oct al 2 nov cuenta entera en
  octubre. Es simple de explicar y coincide con la planilla. (Prorratear por noche sería más exacto, pero no
  se puede explicar en una línea.)
- **Comisiones:** mientras no estén configuradas las reales (SPEC §9), se muestra el ingreso **sin descontar**
  y la línea "Falta configurar la comisión de Booking", en vez de inventar un porcentaje.
- Mes sin reservas: "Todavía no hay reservas en noviembre." y las cifras en $0 / 0%.

### 3. Por canal
- Una fila por canal: **Airbnb**, **Booking** y **Directo** (WhatsApp, Instagram, teléfono y en persona
  juntos: para el mes importa si hay comisión o no).
- Nombre (16px/700) · barra · monto neto (17px/700, alineado a la derecha). Debajo, "43 noches" y, en
  Directo, "sin comisión".
- Ordenadas de mayor a menor. Barras de un **solo color neutro** (los canales no tienen color propio,
  MASTER), `#6B5D50`, proporcionales al canal más alto. 12px de alto, extremo redondeado de 4px y
  base recta. Contraste con la tarjeta ≥ 3:1 (validado). Sin tooltip: el valor ya está escrito al lado. El monto siempre está escrito: la barra solo ayuda a comparar.
- Al pie: "Comisiones del mes: $1.024.750". Solo estadías: las tinajas van en Servicios (no pagan comisión).

### 4. Por cabaña
- Ocupación del mes por unidad, en el orden fijo de Inicio (Anko, Llu Llu, Chañar, Antay, Cachi, Liqui).
- Nombre · barra · porcentaje. Una unidad inactiva (por ejemplo, en renovación) aparece en gris con su motivo en vez de barra.

### 5. Servicios
- **Tinajas:** cantidad de usos del mes (las dos tinajas juntas) · lo cobrado por tinajas.
- **Quincho:** cantidad de usos · "sin costo".
- Cuenta por fecha del servicio (no de la reserva).

### 6. Próximos 7 días
- Una línea: "6 llegadas · 8 salidas · 8 aseos". Solo aparece en el mes actual. El detalle está en Inicio.

### 7. Exportar planilla (Excel)
- Botón **secundario** de ancho completo (52px) con ícono de descarga: **"Descargar planilla de octubre"**.
  El mes del botón cambia con el mes elegido.
- Debajo, 14px: "Excel para la contabilidad. Sin teléfonos ni emails."
- Al tocar: "Preparando planilla…" (botón deshabilitado). Luego el iPhone muestra el archivo
  **`valle-petra-2026-10.xlsx`** con su visor, desde donde se puede guardar en Archivos o enviar.
- Error: aviso rojo "No se pudo crear la planilla. Intenta de nuevo."
- Cada descarga queda en la auditoría (quién y qué mes, SPEC §3.8).

---

## 4. Contenido de la planilla (`.xlsx`)

Una hoja por tema, con la primera fila como encabezado fijo (inmovilizada), filtros activados y columnas de
ancho ajustado. Montos como **números enteros** con formato `$#.##0` (no texto), fechas como **fechas de
Excel** con formato `DD-MM-AAAA`: así se pueden sumar y ordenar.

| Hoja | Una fila por | Columnas |
|---|---|---|
| **Resumen** | cifra | Mes, ingresos brutos, comisiones, ingresos netos, ocupación, noches ocupadas, por cobrar, tinajas (usos y monto), quincho (usos) |
| **Reservas** | reserva que llega en el mes | N° Beds24, canal, cabaña, huésped (nombre), llegada, salida, noches, adultos, niños, estadía, tinajas, total, comisión, neto, pagado, falta, estado |
| **Pagos** | pago con fecha en el mes | Fecha, N° reserva, huésped, tipo (abono/saldo), medio, monto, registró, con comprobante (sí/no) |
| **Servicios** | turno del mes | Fecha, servicio (Quincho / Tinaja 1 / Tinaja 2), desde, hasta, cabaña, huésped, monto |

- **Sin** teléfonos, emails ni comprobantes (minimización, SPEC §6 y Ley 21.719). Las canceladas no van en
  Reservas; sí van sus pagos en Pagos (la plata se recibió).
- **Seguridad: inyección de fórmulas.** Un texto que empieza con `=`, `+`, `-`, `@` (por ejemplo un nombre
  que llega desde Airbnb) se escribe siempre como **texto**, nunca como fórmula. Es un ataque conocido
  ("CSV/formula injection"): una celda que Excel ejecuta al abrir el archivo. Detalle y tests en
  `docs/adr/0007-exportacion-excel-sin-inyeccion-de-formulas.md`.
- **Dependencia prevista:** `openpyxl` (Python puro, sin dependencias nativas). Formato decidido el
  29-10-2026; la dependencia se confirma al implementar (CLAUDE.md: preguntar antes de agregarla).

---

## 5. Estados y errores

| Caso | Qué se ve |
|---|---|
| Cargando | Esqueletos de las tarjetas (sin saltos). |
| Sin conexión | Lo último cargado + "Sin conexión · los datos pueden no estar al día"; el botón de la planilla queda deshabilitado con ese texto. |
| Beds24 atrasado | Línea de frescura en ámbar (§3.1). |
| Comisión sin configurar | Ingresos sin descontar + línea "Falta configurar la comisión de Booking". |

---

## 6. Accesibilidad

- `<h1>` "Dashboard" y `<h2>` por sección. Al cambiar de mes, el nombre del mes se anuncia
  (`aria-live="polite"`).
- Las barras son decorativas (`aria-hidden`): el valor está escrito al lado. Cada fila se lee como
  "Airbnb: 2.745.500 pesos, 38 noches".
- Botones de mes con `aria-label` "Mes anterior" / "Mes siguiente".

---

## 7. Diferencias con MASTER

1. **Ninguna acción principal en terracota en la pantalla.** El único terracota sigue siendo "Nuevo" en la
   barra; "Descargar planilla" es secundario (no es la acción frecuente del día a día).

---

## 8. Datos y reglas (para el backend)

- Todas las cifras se **calculan** al consultar (SPEC §5: nada de saldos guardados). Por cobrar usa la misma
  función que Inicio y la ficha.
- Ingresos por fecha de llegada; ocupación por noches dentro del mes; servicios por fecha del turno.
- Comisión por canal: configuración pendiente (SPEC §9) o el dato que entregue Beds24 (`bookings-financial`).
- La planilla se genera en el servidor, en memoria, y se envía con `Content-Disposition: attachment`; no se
  guarda en disco. Solo usuarios autenticados. Registro en `RegistroAuditoria`.

---

## 9. Pendientes

- Comisiones reales por canal (SPEC §9).
- Comparar con el mismo mes del año anterior: no se incluye por ahora (más números = más confusión).
