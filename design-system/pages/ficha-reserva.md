# Pantalla: Ficha de reserva

> Reglas específicas de esta pantalla. **Prevalecen** sobre `design-system/MASTER.md` solo en lo que se indica
> en la sección "Diferencias con MASTER"; en todo lo demás rige MASTER.

- **Estado:** aprobado (v1, 29-10-2026).
- **Módulo de SPEC:** §3.3 Ficha de reserva.
- **Para qué:** ver **todo** lo de una reserva en un solo lugar y hacer lo que toca **ahora**: registrar la
  llegada, registrar la salida, cobrar lo que falta o escribirle al huésped.
- **Idea central:** la ficha responde tres preguntas, en este orden: **¿hay algo urgente?** (avisos),
  **¿qué hago ahora?** (un solo botón principal, según el momento de la estadía) y **¿cuánto falta pagar?**

---

## 1. Cómo se llega

| Desde | Vuelve a |
|---|---|
| Inicio › tarjeta de "Llegan hoy" / "Salen hoy" (encabezado ›) | Inicio |
| Inicio › Próximos días › fila de llegada o salida | Inicio (con los días que se habían abierto) |
| Nueva reserva › confirmación › "Ver la reserva" | Inicio |
| Registrar pago › después de guardar, si se llegó desde la ficha | La ficha, con el aviso "Pago de $X guardado · Deshacer" |

Botón volver arriba a la izquierda **con texto**: "‹ Inicio". Profundidad máxima 2 (MASTER): desde la ficha solo
se abren hojas (sheets) o Registrar pago, que siempre vuelve a la ficha.

---

## 2. Estructura

```
┌──────────────────────────────────────┐
│ ‹ Inicio            Reserva           │
├──────────────────────────────────────┤
│ ┌ ⚠ No ha pagado nada ──────────────┐ │  1. Avisos (solo si hay)
│ │ "Paga todo al llegar, cliente      │ │
│ │  conocido" · anotó Claudio 29-10   │ │
│ └────────────────────────────────────┘ │
│ Javiera Rivas                  (H1)   │  2. Encabezado
│ Cabaña Antay · Cabaña grande          │
│ [Confirmada] [Abonado · falta $90.000]│
│ [  WhatsApp  ]  [   Llamar   ]        │
│                                       │
│ ┌ Estadía ──────────────────────────┐ │  3. Estadía
│ │ Llegada  30-10-2026 · mañana       │ │
│ │          aprox. 17:00              │ │
│ │ Salida   01-11-2026                │ │
│ │ 2 noches · 2 adultos, 1 niño       │ │
│ │ ✓ Reservada 29-10 · Claudio        │ │
│ │ ○ Llegada                          │ │
│ │ ○ Salida                           │ │
│ └────────────────────────────────────┘ │
│ ┌ Pagos ────────────────────────────┐ │  4. Pagos
│ │ Total             $180.000         │ │
│ │ Pagado             $90.000         │ │
│ │ Falta              $90.000         │ │
│ │ ─────────────────────────────────  │ │
│ │ $90.000 · Abono                  › │ │
│ │ Transferencia · 29-10-2026 · Claudio│ │
│ │ [        Registrar pago          ] │ │
│ └────────────────────────────────────┘ │
│ ┌ Servicios ──────────── + Agregar ─┐ │  5. Servicios
│ │ Tinaja 1 · vie 30 oct 20:00–21:30  │ │
│ └────────────────────────────────────┘ │
│ ┌ Notas ───────────────── Editar ───┐ │  6. Notas
│ │ Celebran aniversario.              │ │
│ └────────────────────────────────────┘ │
│ Más opciones                          │  7. Más opciones
│ Reservó por WhatsApp · N° 4815162     │
├──────────────────────────────────────┤
│ [        Registrar llegada          ] │  Botón principal fijo (si corresponde)
└──────────────────────────────────────┘
```

---

## 3. Secciones

### 1. Avisos (arriba de todo, solo si hay)
Bloques con ícono, color **y** texto (`role="alert"` los rojos). Como mucho se ven los que aplican, en este orden:

| Aviso | Cuándo | Color | Texto |
|---|---|---|---|
| **Bloqueo pendiente** | La reserva directa se guardó pero Beds24 no respondió (ver nueva-reserva §7) | Rojo error | "Las fechas todavía NO están bloqueadas en Airbnb y Booking. Reintentando…" A los 15 min: "Bloquéalas a mano en Beds24". |
| **No ha pagado nada** | Reserva **sin ningún pago** | Rojo "Sin pago" | Título "No ha pagado nada" + la **nota obligatoria** que se escribió al reservar sin abono, con quién y cuándo ("anotó Claudio el 29-10-2026"). Desaparece con el primer pago; la nota sigue en Notas. |
| **Falta cobrar antes de que se vaya** | Estadía **en curso** y falta pagar | Ámbar "Abonado" | "Falta cobrar $90.000 antes de la salida." con enlace "Registrar pago". |
| **Aseo pendiente** | Llega hoy y la cabaña tiene aseo sin terminar | Azul "Aseo" | "Aseo pendiente antes de la llegada." (igual que en Inicio). |

### 2. Encabezado
- **H1 (Fraunces): nombre del huésped.** Es como la familia nombra una reserva ("la de Javiera Rivas").
- Debajo, 18px/700: **cabaña · tipo** ("Cabaña Antay · Cabaña grande").
- Chips (color + ícono + texto, MASTER): **estado de la estadía** (Confirmada / En curso / Terminada) y
  **estado de pago** (Pagado / "Abonado · falta $90.000" / "Sin pago · falta $180.000").
- **Dos botones secundarios lado a lado (52px): "WhatsApp" y "Llamar".** Abren `https://wa.me/56912345678` y
  `tel:+56912345678`. Es lo que más se hace con un huésped: avisarle algo o preguntarle a qué hora llega.

### 3. Estadía
- **Llegada** y **Salida** en DD-MM-AAAA (MASTER: fichas), con una ayuda relativa en texto: "hoy", "mañana",
  "en 5 días", "ayer". Hora aproximada de llegada si se anotó ("aprox. 17:00").
- "2 noches · 2 adultos, 1 niño".
- **Línea de tiempo** de la estadía, tres filas con ícono **y** texto (no solo color):
  - ✓ **Reservada** · fecha · quién la creó (o "Airbnb" / "Booking").
  - ✓ / ○ **Llegada** · cuando se registró: "30-10 · 17:20 · Mamá".
  - ✓ / ○ **Salida** · cuando se registró, y "Aseo creado".
- En la interfaz se dice **llegada / salida**; check-in / check-out solo en el código (igual que Inicio).

### 4. Pagos
- Tres filas grandes: **Total**, **Pagado**, **Falta** (la palabra "saldo" no se usa en pantalla). Si ya está
  todo pagado: "✓ Pagado completo" en verde en vez de "Falta".
- Si hay tinajas, el Total se desglosa en una línea apagada: "Estadía $180.000 + tinajas $25.000".
- Si los pagos superan el total (un extra, permitido en Registrar pago): "Pagado de más: $5.000" en texto
  neutro, sin alarma (SPEC §5 pide alertar; un extra cobrado en el lugar es normal).
- **Lista de pagos**, del más nuevo al más antiguo. Cada fila (botón de 64px, ›): monto + tipo ("Abono" /
  "Saldo"), y debajo medio · fecha · quién lo registró. Si tiene comprobante, un ícono de clip con texto
  "Comprobante".
  - Tocar un pago abre una **hoja** con el detalle: monto, tipo, medio, fecha, quién, nota, **"Ver comprobante"**
    (se abre en el visor del iPhone) y, al final, **"Borrar este pago"** en rojo. Borrar sí pide confirmación
    (registrar-pago §3: pasado el "Deshacer", es destructivo): "¿Borrar el pago de $90.000? El saldo volverá a
    $180.000." · Cancelar / Borrar pago.
  - Sin pagos: "Todavía no hay pagos."
- **"Registrar pago"** (botón secundario, ancho completo): lleva a Registrar pago con **esta reserva ya elegida**
  (registrar-pago §1) y vuelve aquí. Solo aparece en reservas directas con algo por cobrar.
- **Airbnb / Booking:** en vez del botón, el texto "Lo cobra Airbnb. Los pagos llegan solos desde Beds24."
  Registrar un pago a mano queda en Más opciones (excepciones, registrar-pago §2.3). **Excepción:** si tiene
  tinajas por pagar (se cobran en el lugar), "Registrar pago" sí aparece, por ese monto.

### 5. Servicios
- Quincho y tinajas **de esta reserva**: "Tinaja 1 · vie 30 oct · 20:00 – 21:30 · $25.000" (formato de
  lista); el quincho dice "sin costo".
- Acción de texto "+ Agregar" arriba a la derecha: abre Agregar servicio con esta reserva ya elegida
  (`servicios.md`). Tocar un turno abre una hoja con "Quitar servicio" (con Deshacer).
- Sin servicios: "Sin servicios."

### 6. Notas
- El texto de `notas` de la reserva tal cual, en 17px. Acción de texto "Editar" que lo convierte en un
  `textarea` con "Guardar nota" / "Cancelar".
- Si la reserva se creó sin abono, la nota obligatoria está aquí (y arriba como aviso mientras no haya pagos).
  Mientras no haya pagos, **no se puede dejar vacía** (misma regla que al crear).
- Sin notas: "+ Agregar nota".

### 7. Más opciones
Botón de texto que abre una hoja. Va al final porque son cosas poco frecuentes:
- **Editar reserva** (solo directas): abre el resumen de Nueva reserva (paso 4) con los datos de esta reserva y
  "Cambiar" en cada bloque. Cambiar fechas o cabaña vuelve a consultar la disponibilidad en vivo con Beds24.
  En Airbnb / Booking: "Fechas, cabaña y monto se cambian en Beds24." (SPEC §5); en la app solo se editan hora
  aproximada, notas y servicios.
- **Registrar pago** (solo Airbnb / Booking, excepción).
- **Corregir llegada / salida**: deshace la llegada o la salida registrada después de que pasó el "Deshacer"
  (con confirmación; queda en la auditoría).
- **Cancelar reserva** (solo directas y confirmadas, en rojo, al final de la hoja): ver §3.1.

Al pie, en texto apagado 14px: "Reservó por WhatsApp · N° 4815162" (el número es el de Beds24, útil para
buscarla allá).

### Botón principal fijo abajo (uno solo, según el momento)

| Momento | Botón principal | Después |
|---|---|---|
| Confirmada, la llegada es **hoy o ya pasó** | **"Registrar llegada"** | Chip "En curso", línea de tiempo con hora y autor, aviso "Llegada registrada · Deshacer" (8 s). Sin confirmación. |
| Confirmada, la llegada es **futura** | Sin botón. La línea de tiempo dice "Llega en 5 días". | — |
| En curso | **"Registrar salida"** (se puede antes de la fecha de salida: a veces se van antes) | Chip "Terminada", "Salida registrada. Aseo creado." · Deshacer (8 s). |
| Terminada | Sin botón. | — |

Si falta pagar al registrar la salida **no se bloquea**: el aviso ámbar de arriba ya lo dijo y el aviso de
"Salida registrada" agrega "Quedó faltando $90.000".

### 3.1 Cancelar reserva
Libera las fechas en Beds24 para que Airbnb y Booking las puedan vender (aprobado 29-10-2026).

- **Dónde:** Más opciones › "Cancelar reserva", la última opción de la hoja, en rojo. Poco frecuente y
  destructiva: no va a la vista.
- **Cuándo existe:** solo en reservas **directas** y **confirmadas** (todavía no llegan). Si ya llegaron, lo
  que corresponde es "Registrar salida". En Airbnb / Booking la opción no aparece y en su lugar dice "Las
  reservas de Airbnb se cancelan en Airbnb." (la cancelación llega sola por Beds24).
- **Confirmación (excepción a "Deshacer"):** apenas se liberan las fechas otro las puede reservar, así que no
  se puede deshacer con seguridad. Pantalla de confirmación:

```
┌──────────────────────────────────────┐
│ ¿Cancelar la reserva de Javiera Rivas?│
│ Cabaña Antay · 30-10-2026 → 01-11-2026│
│ Las fechas quedarán libres en Airbnb  │
│ y Booking. No se puede deshacer.      │
│                                       │
│ ¿Por qué se cancela?                  │
│ ( ) El huésped canceló                │
│ ( ) No pagó el abono                  │
│ ( ) Se anotó por error                │
│ ( ) Otro motivo  [_______________]    │
│                                       │
│ ⓘ Tenía pagos por $90.000. Quedan     │
│   registrados; si hay que devolver,   │
│   se hace fuera de la app.            │
│                                       │
│ [   Sí, cancelar y liberar fechas   ] │ rojo (destructivo)
│ [        No, volver a la reserva    ] │ secundario
└──────────────────────────────────────┘
```

  - El motivo es **obligatorio**, sin valor por defecto (radios grandes de 56px). "Otro motivo" abre un campo
    de texto obligatorio.
  - El aviso de pagos solo aparece si la reserva tiene pagos.
  - Los botones nunca dicen solo "Cancelar": "No, volver a la reserva" evita la confusión entre cancelar la
    acción y cancelar la reserva.
- **Después de confirmar:** "Cancelando…" (botón deshabilitado). El servidor libera las fechas en Beds24.
  - Bien: la ficha queda con el chip gris **"Cancelada"**, sin botón principal, y el aviso "Reserva cancelada.
    Las fechas quedaron libres en Airbnb y Booking." La línea de tiempo agrega "✕ Cancelada · motivo · quién".
  - Beds24 no respondió: aviso rojo "Las fechas todavía NO se liberaron en Beds24. Reintentando…" (mismo
    mecanismo que "Bloqueo pendiente"); a los 15 min: "Libéralas a mano en Beds24".
- Una reserva cancelada **deja de aparecer en Inicio** y no cuenta en "Por cobrar" ni en el Dashboard.
  Se puede abrir igual desde el historial (cuando exista) y queda en la auditoría.

---

## 4. Estados y errores

| Caso | Qué se ve |
|---|---|
| Cargando | Esqueleto de las secciones (sin saltos de contenido). |
| Sin conexión | Se muestra lo último cargado con la línea "Sin conexión · los datos pueden no estar al día"; los botones que guardan quedan deshabilitados con ese texto. |
| Otra persona cambió la reserva mientras tanto (bloqueo optimista, SPEC §5) | No se guarda. Aviso rojo "Mamá cambió esta reserva hace un momento. Revisa los datos y vuelve a intentar." y la ficha se recarga. |
| La reserva fue cancelada en Airbnb / Booking | Chip gris "Cancelada en Airbnb" arriba, sin botón principal. |
| Error al registrar llegada / salida | Aviso rojo "No se pudo registrar la llegada. Intenta de nuevo." El estado no cambia. |

---

## 5. Accesibilidad

- Un `<h1>` (nombre) y un `<h2>` por sección; el foco entra al `<h1>` al abrir la ficha.
- Teléfono con `aria-label` completo: "Escribir por WhatsApp a Javiera Rivas", "Llamar a Javiera Rivas".
- Línea de tiempo como lista ordenada (`<ol>`) con el estado en texto ("hecho", "pendiente").
- Avisos de Deshacer con `role="status"`; errores con `role="alert"`.
- Montos con el signo `$` y puntos de miles; nunca solo color para "Falta".

---

## 6. Diferencias con MASTER

1. **Sin barra inferior** (Inicio · Nuevo · Dashboard): la ficha es el segundo nivel; en su lugar va el botón
   principal fijo, al alcance del pulgar. "‹ Inicio" arriba vuelve.
2. **Fechas con ayuda relativa** ("mañana", "en 5 días") junto a DD-MM-AAAA. Los turnos usan el formato de
   lista ("sáb 31 oct") porque son una lista.

---

## 7. Datos y reglas (para el backend)

- Todo se lee de `Reserva`, `Huesped`, `Pago` (con `registrado_por`), `TurnoServicio` y `RegistroAuditoria`
  (para "quién y cuándo" de la línea de tiempo). Saldo y estado de pago se **calculan** (SPEC §5).
- Registrar llegada / salida: cambia `estado`, guarda autor y hora en la auditoría, usa `version` (bloqueo
  optimista). La salida crea la `TareaAseo` en la misma transacción.
- Check-out solo después de check-in (SPEC §5): el botón "Registrar salida" no existe antes de la llegada, y el
  servidor lo valida igual.
- Reservas de Airbnb / Booking: los campos sincronizados son de solo lectura en la app (SPEC §5).
- Comprobantes servidos solo a usuarios autenticados (SPEC §6).
- Cancelar: `estado = cancelada`, `motivo_cancelacion` (obligatorio), `cancelada_por`, `cancelada_en`. Solo
  reservas directas en estado confirmada (se valida en el servidor). La liberación en Beds24 sigue el mismo
  patrón que la creación: estado intermedio "Liberación pendiente" + reintentos, nunca una reserva cancelada
  que siga bloqueando sin aviso.
- Los enlaces de WhatsApp y llamada se arman con el teléfono en E.164; no se envía nada desde el servidor.

---

## 8. Pendientes

- ~~Cancelar reserva~~ → **aprobado** (29-10-2026): en Más opciones, con confirmación y motivo (§3.1).
- Devoluciones de dinero al cancelar: por ahora fuera de la app.
- ~~Flujo de "+ Agregar turno"~~ → especificado en `servicios.md` (quincho y tinajas).
