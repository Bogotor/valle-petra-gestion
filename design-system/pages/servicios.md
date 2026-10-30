# Pantalla: Agregar servicio (quincho y tinajas)

> Reglas específicas de esta pantalla. **Prevalecen** sobre `design-system/MASTER.md` solo en lo que se indica
> en la sección "Diferencias con MASTER"; en todo lo demás rige MASTER.

- **Estado:** aprobado (v1, 29-10-2026).
- **Módulo de SPEC:** §3.7 Operaciones (servicios con turno).
- **Para qué:** anotar que un huésped usará el **quincho** o una **tinaja**, en qué día y en qué horario, sin
  que dos huéspedes queden en el mismo recurso a la misma hora.
- **Decisiones (29-10-2026):**
  - Recursos: **Quincho** (1) y **Tinaja 1** y **Tinaja 2**. Ya no hay hot tub.
  - **Solo huéspedes**: cada turno pertenece a una reserva.
  - **El quincho no se cobra. La tinaja sí**, y su precio **se suma a la reserva**: aumenta lo que falta pagar
    y se paga con Registrar pago, como el resto. Un solo saldo por huésped.
  - **Hora libre**: se elige hora de inicio y de término; la app solo impide que se cruce con otro turno del
    mismo recurso.
  - **Horario de funcionamiento: de 10:00 a 23:00** (quincho y tinajas).
  - **Precio habitual de la tinaja: $20.000 a $25.000 cada 30 minutos.**

---

## 1. Cómo se llega

| Desde | Qué viene elegido | Vuelve a |
|---|---|---|
| Ficha de reserva › Servicios › "+ Agregar" | La reserva | La ficha |
| Inicio › Servicios de hoy › "+ Agregar servicio" | Nada (se elige la reserva primero) y el día = hoy | Inicio |

Una sola pantalla (no pasos): son pocos datos. Barra superior: "Cancelar" · "Agregar servicio". Sin barra
inferior; botón principal fijo abajo.

---

## 2. Estructura

```
┌──────────────────────────────────────┐
│ Cancelar      Agregar servicio        │
├──────────────────────────────────────┤
│ Pedro Rojas · Cabaña Antay            │  0. Para quién (solo lectura si viene de la ficha)
│ 29-10-2026 → 31-10-2026               │
│                                       │
│ ¿Qué servicio?                        │  1. Servicio
│ [  Tinaja  ]  [  Quincho  ]           │
│                                       │
│ ¿Qué día?                             │  2. Día (solo los días de la estadía)
│ [jue 29 oct] [vie 30 oct] [sáb 31 oct]│
│                                       │
│ ¿A qué hora?                          │  3. Horario
│ Desde [20:00]     Hasta [21:30]       │
│                                       │
│ ¿Cuál tinaja?                         │  4. Recurso (solo tinaja)
│ (•) Tinaja 1 · libre en ese horario   │
│     Tinaja 2 · ocupada 20:00 – 22:00  │
│              (Domo Llu Llu)           │
│                                       │
│ ¿Cuánto cuesta?          $ [     0 ]  │  5. Precio (solo tinaja)
│ Se suma a lo que falta pagar de la    │
│ reserva.                              │
├──────────────────────────────────────┤
│ [       Guardar tinaja $25.000      ] │
└──────────────────────────────────────┘
```

---

## 3. Campos

### 0. Para quién
- Desde la ficha: tarjeta de solo lectura "Pedro Rojas · Cabaña Antay" y las fechas de la estadía.
- Desde Inicio: lista de reservas **alojadas hoy o que llegan hoy** (misma tarjeta que Registrar pago paso 0),
  con buscador si hay más de 6. Tocar una la elige y muestra el formulario.

### 1. ¿Qué servicio?
- Dos botones grandes (≥ 56px, `aria-pressed`): **Tinaja** · **Quincho**. **Sin valor por defecto.**
- Se elige "Tinaja", no "Tinaja 1" / "Tinaja 2": cuál de las dos se decide en el punto 4, viendo cuál está
  libre.

### 2. ¿Qué día?
- Chips con **solo los días de la estadía** (de la llegada a la salida), en formato de lista ("vie 30 oct").
  Si se entra desde Inicio, viene marcado **hoy**.
- Los días pasados de la estadía no aparecen.

### 3. ¿A qué hora?
- Dos campos lado a lado, **Desde** y **Hasta**, con el selector nativo de hora (`type="time"`, 24 h).
  Vacíos.
- Debajo, en vivo: "1 hora 30 minutos".
- "Hasta" igual o antes de "Desde": error rojo "La hora de término debe ser después del inicio".
- Fuera del horario de funcionamiento (antes de 10:00 o después de 23:00): error rojo "El quincho y las
  tinajas funcionan de 10:00 a 23:00". Bloquea el guardado.
- Para ayudar a elegir, debajo del horario se ve **lo ocupado ese día** del servicio elegido:
  "Ocupado ese día: Tinaja 2 · 20:00 – 22:00 (Domo Llu Llu)". Si no hay nada: "Libre todo el día".

### 4. ¿Cuál tinaja? (solo tinaja)
- Aparece cuando hay servicio, día y horario válidos. Mismo patrón que las cabañas de Nueva reserva:
  - **Libres** como radios grandes: "Tinaja 1 · libre en ese horario".
  - **Ocupadas** en gris, como texto (no como botón deshabilitado), con el motivo: "Tinaja 2 · ocupada
    20:00 – 22:00 (Domo Llu Llu)".
  - Si hay una sola libre, **viene elegida** (no hay nada que decidir).
  - Ninguna libre: aviso rojo "Las dos tinajas están ocupadas en ese horario. Prueba otra hora." y Guardar
    bloqueado.
- **Quincho** (uno solo): no hay elección; si se cruza, aviso rojo "El quincho está ocupado de 13:00 a 17:00
  (Cabaña Chañar). Prueba otra hora." y Guardar bloqueado.

### 5. ¿Cuánto cuesta? (solo tinaja)
- Campo de monto igual que en Registrar pago: **vacío** con "0" gris, `$` fuera del campo,
  `inputmode="numeric"`, puntos de miles automáticos. Obligatorio y > 0 (MASTER: los montos nunca vienen
  prellenados).
- **Ayuda con el precio habitual** (sin prellenar el campo): "Lo habitual: $20.000 a $25.000 cada 30 min.
  Para 1 h 30 min: entre $60.000 y $75.000." Las medias horas se cuentan redondeando hacia arriba
  (45 min = 2 medias horas).
- Si el monto escrito queda **fuera** de ese rango: aviso ámbar (no bloquea) "Revisa el monto: lo habitual para
  1 h 30 min es entre $60.000 y $75.000." Sirve para atrapar un cero de más o de menos.
- Debajo: "Se suma a lo que falta pagar de la reserva." y, en vivo, "Faltará pagar: $115.000".
- Quincho: en su lugar, la línea "El quincho no se cobra."

### Botón principal (fijo abajo)
- **"Guardar tinaja $25.000"** / **"Guardar quincho"**.
- Siempre se puede tocar: si falta algo, no guarda y marca en rojo cada campo pendiente ("Elige un servicio",
  "Elige el día", "Escribe la hora", "Escribe cuánto cuesta").

---

## 4. Después de guardar

1. "Guardando…" (botón deshabilitado). El servidor vuelve a revisar que no se cruce (otra persona pudo
   anotar el mismo recurso recién).
2. Vuelve al origen con el aviso **"Tinaja 1 agregada · vie 30 oct 20:00 · Deshacer"** (8 s).
3. En la ficha, el turno aparece en Servicios y "Falta" sube en $25.000; el chip de pago se recalcula
   (una reserva "Pagado" pasa a "Abonado · falta $25.000").
4. Si se cruzó mientras tanto: no se guarda; aviso rojo "Tinaja 1 se acaba de ocupar en ese horario. Elige
   otra tinaja u otra hora." Lo escrito se conserva.

### Quitar un servicio
- En la ficha (y en Inicio), tocar un turno abre una hoja con el detalle y **"Quitar servicio"** (rojo).
  Se quita sin confirmación, con "Servicio quitado · Deshacer" (8 s): es reversible y el recurso no lo
  toma nadie más en esos segundos.
- Si la tinaja ya estaba pagada, quitarla deja "Pagado de más" en la ficha (la devolución, fuera de la app).

---

## 5. Reservas de Airbnb y Booking

- La tinaja se cobra en el lugar (no pasa por la plataforma). Al agregarla, la reserva queda con algo por
  cobrar, así que **"Registrar pago" aparece en su ficha y en Inicio** aunque sea de Airbnb o Booking (solo
  por el monto de los servicios). Actualiza registrar-pago §2.3 y ficha-reserva §3.4.

---

## 6. Accesibilidad

- Cada pregunta es un `<fieldset>` con `<legend>` ("¿Qué servicio?").
- Recursos ocupados como texto, no como controles deshabilitados.
- Errores con `role="alert"` junto al campo.

---

## 7. Diferencias con MASTER

1. **Sin barra inferior**: es un flujo con principio y fin (igual que Registrar pago y Nueva reserva).

---

## 8. Datos y reglas (para el backend)

- `Servicio`: nombre (Quincho, Tinaja 1, Tinaja 2), tipo (`quincho` / `tinaja`), se_cobra (bool), activo.
- `TurnoServicio`: servicio (FK, PROTECT), reserva (FK, obligatoria), inicio, fin (fecha y hora), monto (CLP
  entero; > 0 si el servicio se cobra, 0 si no), creado_por, timestamps.
- **Sin solape por recurso en la base de datos:** `ExclusionConstraint` sobre servicio + rango
  `[inicio, fin)` (igual que las reservas por unidad). Dos tinajas distintas sí pueden usarse a la misma hora.
- `fin > inicio` (`CheckConstraint`). Dentro del horario de funcionamiento (10:00–23:00, configurable). El turno dentro de las fechas de la reserva y el monto según
  `se_cobra` dependen de otras tablas: se validan en el servicio dentro de la transacción.
- **Lo que falta pagar** = monto de la estadía + Σ montos de sus servicios − Σ pagos. Se calcula, nunca se
  guarda (SPEC §5).
- Auditoría al agregar y al quitar.

---

## 9. Pendientes

- ~~Precio de la tinaja~~ → se escribe cada vez, con la ayuda del precio habitual ($20.000 a $25.000 cada
  30 min) y aviso ámbar si queda fuera de rango (29-10-2026). El rango vive en configuración, no en el código.
- ~~Horario de funcionamiento~~ → 10:00 a 23:00 (29-10-2026). También en configuración.
