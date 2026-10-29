# Pantalla: Registrar pago

> Reglas específicas de esta pantalla. **Prevalecen** sobre `design-system/MASTER.md` solo en lo que se indica
> en la sección "Diferencias con MASTER"; en todo lo demás rige MASTER.

- **Estado:** aprobado (v2, tras probar el prototipo v0.2).
- **Módulo de SPEC:** §3.5 Registrar pago.
- **Objetivo:** anotar un pago recibido de forma simple: **elegir la reserva → escribir el monto → guardar**.
  La fecha y el medio vienen por defecto; el monto lo escribe siempre la persona.

### Historial
- **v1:** desde Por cobrar se saltaba la lista y el monto venía prellenado con el saldo.
- **v2:** siempre se parte por la lista de reservas con saldo pendiente, y el monto empieza vacío (muestra "0" en
  gris). Al probar el prototipo, un monto ya escrito podía confundir: se corre el riesgo de guardar sin mirarlo.
- **Regla de seguridad:** es un **registro manual**. Nunca se piden ni se guardan números de tarjeta ni datos de
  cuentas bancarias.

---

## 1. Cómo se llega

| Desde | Primer paso | Toques hasta guardar |
|---|---|---|
| Inicio › Por cobrar › "Registrar pago" | Lista de reservas con saldo pendiente (paso 0) | elegir + escribir monto + guardar |
| Barra › "Nuevo" › "Registrar pago" | Igual | 1 más (abrir "Nuevo") |
| Ficha de reserva › "Registrar pago" | Directo al formulario de esa reserva (ya está elegida) | escribir monto + guardar |

### Paso 0: elegir la reserva (desde Inicio y desde "Nuevo")

```
┌──────────────────────────────────────┐
│ Cancelar      Registrar pago          │
├──────────────────────────────────────┤
│ ¿De qué reserva es el pago?           │
│ [ Buscar por nombre…              ]   │
│ Falta pagar                           │
│ ┌ Pedro Rojas                       › ┐│
│ │ Cabaña Antay · llega hoy            ││
│ │ (Abonado · falta $90.000)           ││
│ └──────────────────────────────────────┘│
│ ┌ Javiera Rivas                     › ┐│
│ │ Domo Llu Llu · domingo 1 nov        ││
│ │ (Abonado · falta $60.000)           ││
│ └──────────────────────────────────────┘│
│ Mostrar todas las reservas            │
└──────────────────────────────────────┘
```

- Primero, **solo las reservas con saldo pendiente**, ordenadas por fecha de llegada (las más próximas arriba):
  es casi siempre la que se busca.
- Buscador por nombre del huésped (`type="search"`, 16px). "Mostrar todas las reservas" abre el resto
  (divulgación progresiva).
- Tocar una reserva lleva al formulario de esa reserva, con el monto vacío.

---

## 2. Formulario

```
┌──────────────────────────────────────┐
│ Cancelar      Registrar pago          │
├──────────────────────────────────────┤
│ Cabaña Antay · Pedro Rojas            │  1. Resumen de la reserva
│ jueves 29 oct → sábado 31 oct         │
│ Total $180.000 · Pagado $90.000       │
│ Falta pagar $90.000                   │
├──────────────────────────────────────┤
│ ¿Cuánto pagó?                         │  2. Monto
│ ┌ $ │ 0  (gris, vacío)              ┐ │
│ [Todo lo que falta · $90.000]         │
│ ✓ Con este pago queda pagado          │
├──────────────────────────────────────┤
│ ¿Cómo pagó?                           │  3. Medio
│ [Transferencia] [Efectivo]            │
│ [Webpay]        [Mercado Pago]        │
├──────────────────────────────────────┤
│ Fecha del pago                        │  4. Fecha
│ [ Hoy · 29-10-2026              ▾ ]   │
├──────────────────────────────────────┤
│ + Agregar comprobante o nota          │  5. Opcionales (cerrados)
├──────────────────────────────────────┤
│ [        Guardar pago $90.000       ] │  Botón fijo abajo
│ No se guardan datos de tarjeta        │
└──────────────────────────────────────┘
```

### 1. Resumen de la reserva (solo lectura)
- Unidad · huésped, rango de fechas en formato de lista, total, pagado y **"Falta pagar $90.000"** destacado.
- Un enlace "Cambiar reserva" vuelve al paso 0.

### 2. Monto
- Etiqueta en lenguaje simple: **"¿Cuánto pagó?"**.
- Campo grande (28px/700, `tabular-nums`), `$` fuera del campo, `type="text"` + `inputmode="numeric"` +
  `autocomplete="off"`. Los puntos de miles aparecen solos al escribir.
- **Empieza vacío**, con "0" en gris como referencia (placeholder). Nunca viene escrito: lo escribe la persona.
- Atajo opcional: el botón **"Todo lo que falta · $90.000"** escribe el saldo en 1 toque, solo si la persona lo toca.
- **Abono o saldo se deduce solo; no se pregunta.** Si el monto cubre lo que falta, se guarda como *saldo*;
  si es menor, como *abono*. (SPEC pide el tipo; el usuario no tiene que elegirlo: un campo menos.)
- Debajo, una línea que se actualiza al escribir:
  - Cubre el saldo: "✓ Con este pago queda **pagado**" (verde de "Pagado").
  - Menor: "Quedará faltando **$30.000**" (texto secundario).
  - Mayor que lo que falta: aviso ámbar "Es más de lo que falta ($90.000). Revisa el monto." — **no bloquea**
    (SPEC: alertar, no impedir), pero el botón cambia a "Guardar igual $120.000".
- Monto vacío o $0: error bajo el campo al intentar guardar: "Escribe el monto que pagó". (La base de datos
  también exige monto > 0.)

### 3. Medio de pago
- Etiqueta **"¿Cómo pagó?"**. Botones grandes seleccionables (≥ 56px, `aria-pressed`), no lista desplegable.
- Opciones (cuadrícula de 2×2): **Transferencia · Efectivo · Webpay · Mercado Pago**.
  Webpay y Mercado Pago son los links de pago con tarjeta; elegirlos no abre ningún campo, solo deja constancia
  del medio y del proveedor (útil para conciliar más adelante).
- **Medio por defecto fijo: Transferencia** (decidido 29-10-2026). No se recuerda el último usado: el formulario
  se ve siempre igual, sin depender de lo que hizo otra persona.
- **Reservas de Airbnb y Booking:** su pago lo cobra la plataforma y debería llegar sincronizado desde Beds24,
  no registrarse a mano. En esas reservas, "Registrar pago" no aparece en Inicio ni en la ficha; solo queda
  accesible desde la ficha, en "Más opciones", para casos excepcionales (por ejemplo, un cobro extra en el lugar).
  *Por verificar en la fase 3:* qué datos de pago entrega la API V2 de Beds24 (requiere el permiso
  `bookings-financial`).

### 4. Fecha
- Muestra "Hoy · 29-10-2026"; tocarla abre el selector nativo de iOS (`type="date"`).
- Fecha futura: aviso "La fecha es posterior a hoy" (no bloquea: puede ser una transferencia programada).

### 5. Opcionales: comprobante y nota (cerrados por defecto)
- Un botón de texto **"+ Agregar comprobante o nota"** los despliega (divulgación progresiva).
- **Comprobante:** botón "Adjuntar foto o PDF" (cámara o archivos del iPhone). Muestra miniatura y "Quitar".
  Texto: "Solo lo ven los usuarios de la app". Se guarda en almacenamiento privado (SPEC §6).
  Lo pueden ver **todos los usuarios autenticados** (decidido 29-10-2026).
- **Nota:** `textarea` de 16px, ejemplo en el placeholder: "Ej: transfirió desde la cuenta de su pareja".

### Botón principal (fijo abajo)
- **"Guardar pago $90.000"**: el monto en el botón confirma qué se va a guardar sin otra pantalla.
- Debajo, en 14px: "Registro manual. No se guardan datos de tarjeta."

---

## 3. Después de guardar

1. Botón deshabilitado + "Guardando…" (evita el doble toque).
2. Vuelve a la pantalla de origen (Inicio o la ficha) con el aviso **"Pago de $90.000 guardado · Deshacer"** (8 s).
3. El saldo y el chip de pago se actualizan; en Inicio, si no queda nada por cobrar, la sección desaparece.
4. "Deshacer" elimina el pago recién creado (queda en el registro de auditoría). Pasado ese tiempo, borrar un pago
   es una acción destructiva desde la ficha, con confirmación.

---

## 4. Estados y errores

| Situación | Qué se ve |
|---|---|
| Sin conexión | Banda "Sin conexión". Botón "Sin conexión · no se puede guardar"; lo escrito no se pierde. |
| Error del servidor | Aviso rojo sobre el botón: "No se pudo guardar el pago. Intenta de nuevo." El formulario queda igual. |
| Otro usuario registró un pago en la misma reserva mientras tanto | "Alguien registró un pago en esta reserva hace un momento. Ahora falta $X." y se recalcula lo que falta antes de guardar (bloqueo optimista, SPEC §5). |
| Comprobante muy grande o de tipo no permitido | "El archivo debe ser una foto o un PDF de hasta 10 MB." |
| Cancelar con datos escritos | Si escribió un monto o cambió algo: "¿Salir sin guardar el pago?" (Salir / Seguir editando). Si no cambió nada, sale directo. |

---

## 5. Accesibilidad

- Cada campo con `<label>` visible; los grupos de botones (medio) con `role="group"` + `aria-labelledby`.
- La línea "Con este pago queda pagado / Quedará faltando" es `aria-live="polite"`.
- Errores con `role="alert"` y asociados al campo con `aria-describedby`.
- Orden de foco: monto → medio → fecha → opcionales → guardar.
- Al abrir el formulario, el foco va al monto y se abre el teclado numérico: es lo primero que hay que escribir.

---

## 6. Diferencias con MASTER

1. **Sin barra inferior.** Es un flujo con principio y fin: se oculta la barra para dar espacio al botón "Guardar"
   y evitar salir a mitad de camino por accidente. Se sale con "Cancelar" (arriba a la izquierda, con texto).

---

## 7. Datos y reglas (para el backend)

- Entrada: id de reserva; servidor devuelve total, suma de pagos, **saldo calculado** (nunca guardado), canal y
  `version` de la reserva.
- Al guardar: monto (CLP entero > 0), medio, fecha, tipo (derivado: saldo si cubre lo que falta, si no abono),
  comprobante opcional, nota opcional, `registrado_por` = usuario de la sesión.
- **Pagos duplicados:** el formulario lleva un token único por intento; el servidor rechaza un segundo envío con el
  mismo token (protege contra doble toque o reintentos de red).
- Validaciones en servidor y en base de datos (no solo en el formulario): monto > 0, reserva existente, fecha válida.
- Registro de auditoría al crear y al deshacer/borrar.

## 8. Pendientes

- ~~Último medio usado~~ → **medio fijo por defecto: Transferencia** (decidido 29-10-2026).
- ~~Límite del comprobante~~ → **10 MB**; foto JPG/PNG/HEIC y PDF (decidido 29-10-2026).
- ~~Quién ve los comprobantes~~ → **todos los usuarios autenticados** (decidido 29-10-2026).
