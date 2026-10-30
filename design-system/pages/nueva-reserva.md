# Pantalla: Nueva reserva directa

> Reglas específicas de esta pantalla. **Prevalecen** sobre `design-system/MASTER.md` solo en lo que se indica
> en la sección "Diferencias con MASTER"; en todo lo demás rige MASTER.

- **Estado:** aprobado (v1).
- **Módulo de SPEC:** §3.4 Nueva reserva directa.
- **Para qué:** reservas que llegan por WhatsApp, Instagram, teléfono o en persona (no por Airbnb ni Booking).
- **Efecto clave:** al guardar, la reserva se envía a Beds24 y **bloquea esas fechas en Airbnb y Booking**.

---

## 1. Enfoque: por pasos, no un formulario largo

El formulario del prototipo v0.1 medía 2.160 px: demasiado para usuarios de 60+. Se divide en **4 pasos
cortos**, cada uno con una sola pregunta grande, y **primero las fechas y la cabaña**: así nunca se llenan los
datos del huésped para después descubrir que la cabaña está ocupada.

| Paso | Pregunta | Qué se completa |
|---|---|---|
| 1 | **¿Cuándo y dónde?** | Llegada, salida y cabaña (solo se pueden elegir las libres) |
| 2 | **¿Quién viene?** | Nombre, teléfono, email (opcional), adultos y niños, hora aproximada de llegada (opcional), por dónde reservó |
| 3 | **¿Cuánto paga?** | Total de la estadía y abono |
| 4 | **Revisa y guarda** | Resumen de todo, con "Cambiar" en cada parte |

Barra superior en todos los pasos: **"‹ Atrás"** (o "Cancelar" en el paso 1), el título "Nueva reserva" y
el progreso en texto: **"Paso 2 de 4"**. Botón principal fijo abajo: **"Siguiente"** (en el paso 4:
**"Guardar reserva"**).

---

## 2. Cómo se llega

| Desde | Qué viene prellenado |
|---|---|
| Barra › "Nuevo" › "Nueva reserva" | Nada |
| Inicio › Próximos días › botón de cabaña libre ("Cabaña Antay +") | Cabaña y fecha de llegada |

---

## 3. Paso 1 · ¿Cuándo y dónde?

```
┌──────────────────────────────────────┐
│ Cancelar     Nueva reserva  Paso 1 de 4│
├──────────────────────────────────────┤
│ ¿Cuándo y dónde?                      │
│ Llegada              Salida           │
│ [ viernes 30 oct ▾ ] [ domingo 1 nov ▾]│
│ 2 noches                              │
│                                       │
│ Cabañas libres esas fechas            │
│ ┌ ◉ Cabaña Antay   (Cabaña grande)    ┐│
│ │   Hasta 5 personas                  ││
│ └──────────────────────────────────────┘│
│ ┌ ○ Domo Llu Llu   (Domo)             ┐│
│ │   Hasta 3 adultos, o 2 adultos y    ││
│ │   2 niños                           ││
│ └──────────────────────────────────────┘│
│ Ocupadas esas fechas                  │
│   Domo Anko · ocupado hasta dom 1 nov │
│   Cabaña Chañar · ocupada             │
│   Cabaña Cachi · ocupada              │
├──────────────────────────────────────┤
│ [            Siguiente              ] │
└──────────────────────────────────────┘
```

- **Fechas:** dos campos, **Llegada** y **Salida**, uno al lado del otro ("Elegir" si están vacíos; si no,
  la fecha en formato de lista, `viernes 30 oct`). Al tocar cualquiera se abre **un solo calendario** del mes
  (lunes a domingo, días de 48 px, "‹ ›" para cambiar de mes) que elige el **rango en dos toques**:
  - El **primer toque es la llegada**; el calendario sigue abierto y dice "Ahora toca el día de salida".
  - El **segundo toque es la salida**; el calendario se cierra solo y aparece "2 noches".
  - Si el segundo toque es igual o anterior a la llegada, ese día pasa a ser la nueva llegada (no hay error
    posible de "salida antes de la llegada").
  - Tocar "Salida" con llegada ya elegida permite cambiar solo la salida.
  - Hoy va marcado con un borde; el rango elegido, con fondo terracota claro y los extremos en terracota.
  - No se usa el selector nativo `type="date"` porque obliga a abrir dos selectores distintos.
  - Llegada en el pasado: aviso "La llegada ya pasó. ¿Es correcto?" (no bloquea: puede ser una reserva atrasada
    de anotar).
- **Cabañas:** aparecen recién cuando hay fechas válidas. Se consulta la disponibilidad **en vivo**.
  - **Libres:** tarjetas seleccionables grandes (≥ 64 px, `<input type="radio">` con su `<label>`), con:
    - el nombre y un **chip de tipo** (neutro, con texto): **"Domo"**, **"Cabaña chica"** o **"Cabaña grande"**;
    - la capacidad en palabras: "Hasta 3 adultos, o 2 adultos y 2 niños" / "Hasta 5 personas".
  - **Ocupadas:** lista gris aparte, **no seleccionable**, con el motivo en texto: "ocupado hasta domingo 1 nov".
    Así se entiende por qué no se puede elegir, sin mensajes de error.
  - Ninguna libre: "No hay cabañas libres esas fechas. Prueba con otras fechas."
- Si se llegó con cabaña y llegada prellenadas, solo falta elegir la salida. Si con esa salida la cabaña deja de
  estar libre: "Cabaña Antay está ocupada desde el lunes 2 nov. Elige una salida anterior u otra cabaña."
- "Siguiente" se habilita con fechas válidas y una cabaña elegida.

## 4. Paso 2 · ¿Quién viene?

- **Nombre y apellido** (`autocomplete="off"`, 16px+). Ejemplo en el placeholder: "Ej: Javiera Rivas".
  - Solo letras (con tildes y ñ), espacios, guion, apóstrofo y punto. Si se escribe un número o un símbolo,
    aviso rojo **al momento**: "Usa solo letras: el nombre no puede llevar números ni símbolos". Vacío al
    tocar Siguiente: "Escribe el nombre del huésped". La misma regla se valida en el servidor.
- **Teléfono / WhatsApp:** prefijo **+56** fijo a la izquierda del campo, `type="tel"`, `inputmode="tel"`.
  Formato automático al escribir: `9 1234 5678`. Se guarda en E.164 (`+56912345678`).
  - Número incompleto: "Faltan números: el celular tiene 9 dígitos".
- **Email (opcional)**: campo visible, `type="email"`, con "(opcional)" en la etiqueta. Si se escribe, se valida
  el formato: "Revisa el email, parece incompleto".
- **¿Cuántas personas?** Dos contadores, uno debajo del otro, con botones **−** y **+** de 56 px y el número
  grande en el medio (`aria-live`):
  - **Adultos**: empieza en 2, mínimo 1.
  - **Niños**: empieza en 0, mínimo 0. Debajo: "Hasta 12 años".
  - Arriba de los contadores, la regla de la cabaña elegida en palabras:
    "Domo Llu Llu recibe hasta 3 adultos, o 2 adultos y 2 niños."
  - **No se puede pasar del máximo** (ver §4.1): el botón **+** que superaría la capacidad queda deshabilitado
    y aparece un aviso **rojo** (`role="alert"`) que explica por qué, por ejemplo:
    "Con 3 adultos el domo no admite niños. Para traer niños, máximo 2 adultos."
- **Hora aproximada de llegada (opcional)**: campo visible con el selector nativo (`type="time"`, 24 h).
  Vacío por defecto.
- **¿Por dónde reservó?** Botones grandes (2×2): **WhatsApp · Instagram · Teléfono · En persona**.
  **Sin valor por defecto**: hay que elegir uno (1 toque), para no guardar un canal equivocado sin darse cuenta.

### 4.1 Capacidad por tipo de cabaña

| Tipo | Cabañas | Regla | Ejemplos permitidos | No permitido |
|---|---|---|---|---|
| **Domo** | Anko, Llu Llu | Hasta 3 adultos sin niños, **o** hasta 2 adultos con hasta 2 niños | 3+0, 2+2, 2+1, 1+2 | 3+1, 2+3, 4+0 |
| **Cabaña chica** | Chañar, Cachi | Igual que el domo | 3+0, 2+2 | 3+1, 4+0 |
| **Cabaña grande** | Antay, Liqui | Hasta 5 personas en total, cualquier combinación | 5+0, 3+2, 1+4 | 4+2, 6+0 |

Siempre al menos 1 adulto. Si se cambia de cabaña en el paso 1 y las personas ya elegidas no caben, el paso 2
muestra el aviso rojo y **"Siguiente" queda bloqueado** hasta ajustar los contadores.

## 5. Paso 3 · ¿Cuánto paga?

- Arriba, recordatorio: "Cabaña Antay · viernes 30 oct → domingo 1 nov · 2 noches".
- **¿Cuánto cuesta la estadía en total?** Campo de monto igual que en Registrar pago: **vacío** con "0" gris,
  `$` fuera del campo, `inputmode="numeric"`, puntos de miles automáticos. Obligatorio y > 0.
- **¿Dejó un abono?** Dos botones grandes: **"Sí, dejó un abono"** / **"No, reservar sin abono"**.
  - Lo normal es reservar **con** abono (la reserva se gestiona cuando se recibe un monto). La opción sin abono
    existe para excepciones.
  - Si se elige **No**: aparece de inmediato el campo **"¿Por qué no dejó abono?"** (nota **obligatoria**,
    ayuda: "Obligatorio. Se verá como aviso en la ficha de la reserva."; ej: "Paga todo al llegar, cliente
    conocido"). Vacío al tocar Siguiente: borde y texto rojo "Escribe por qué no dejó abono". Sin aviso ámbar.
    En el resumen (paso 4) se ven el chip "Sin pago" y la nota; en Inicio, el chip "Sin pago"; en la **ficha
    de la reserva**, la nota como aviso destacado mientras la reserva siga sin pagos.
  - Sin valor por defecto: hay que elegir una de las dos.
  - Si **Sí**: se despliegan, con las mismas reglas de Registrar pago (la **fecha del abono es siempre hoy**:
    la reserva se confirma cuando se recibe el abono):
    - **¿Cuánto abonó?** (vacío, "0" gris).
    - **¿Cómo pagó?** Transferencia · Efectivo · Webpay · Mercado Pago (Transferencia por defecto).
    - "+ Agregar comprobante (opcional)": foto o PDF hasta 10 MB.
    - Abono mayor que el total: aviso **rojo** "El abono es mayor que el total"; no bloquea (igual que en pagos).
- Debajo, en vivo: **"Saldo al llegar: $90.000"** o "✓ Queda pagado".
- "+ Agregar nota (opcional)": `textarea` 16px. Ej: "Celebran aniversario, piden leña extra". Solo con abono;
  sin abono la nota ya es obligatoria (arriba) y es el mismo campo `notas` de la reserva.

## 6. Paso 4 · Revisa y guarda

```
┌──────────────────────────────────────┐
│ ‹ Atrás      Nueva reserva  Paso 4 de 4│
├──────────────────────────────────────┤
│ Revisa la reserva                     │
│ ┌ Cabaña Antay                Cambiar ┐│
│ │ viernes 30 oct → domingo 1 nov      ││
│ │ 2 noches                            ││
│ ├ Javiera Rivas               Cambiar ┤│
│ │ +56 9 1234 5678 · 2 adultos, 1 niño ││
│ │ Reservó por WhatsApp                ││
│ ├ Total $180.000              Cambiar ┤│
│ │ Abono $90.000 · Transferencia       ││
│ │ Saldo al llegar $90.000             ││
│ └──────────────────────────────────────┘│
│ ⓘ Al guardar se bloquean estas fechas │
│   en Airbnb y Booking.                │
├──────────────────────────────────────┤
│ [          Guardar reserva          ] │
└──────────────────────────────────────┘
```

- Un bloque por paso, cada uno con **"Cambiar"** (48 px) que lleva a ese paso y vuelve aquí al terminar.
- Aviso informativo (azul de "Confirmada"): "Al guardar se bloquean estas fechas en Airbnb y Booking."

## 7. Después de guardar

1. Botón deshabilitado + **"Guardando…"**.
2. El servidor **vuelve a confirmar la disponibilidad con Beds24** (no solo con la base local, que puede tener
   hasta ~2 min de atraso por el polling) y crea la reserva allá.
3. Resultado:

| Caso | Qué se ve |
|---|---|
| Todo bien | Pantalla de confirmación: "Reserva guardada" · "Las fechas quedaron bloqueadas en Airbnb y Booking." · botones **"Ver la reserva"** y **"Volver a Inicio"**. |
| La cabaña se ocupó mientras tanto (llegó una reserva de Airbnb/Booking) | **No se guarda.** Vuelve al paso 1 con aviso rojo: "Cabaña Antay se acaba de ocupar en esas fechas. Elige otra cabaña u otras fechas." Lo demás escrito se conserva. |
| Se guardó en la app pero Beds24 no respondió | Se guarda con estado **"Bloqueo pendiente"** y se reintenta solo. Aviso rojo en la confirmación, en Inicio y en la ficha hasta que se confirme: "Las fechas todavía NO están bloqueadas en Airbnb y Booking. Reintentando…". Si pasan 15 min sin lograrlo: "Bloquéalas a mano en Beds24". |
| Sin conexión | Botón "Sin conexión · no se puede guardar". Lo escrito no se pierde. |

## 8. Salir a mitad de camino

- "Cancelar" (paso 1) o cerrar con datos escritos: "¿Salir sin guardar la reserva?" (Seguir editando / Salir).
- "‹ Atrás" nunca borra lo escrito.

---

## 9. Accesibilidad

- Cada paso es una vista con su `<h1>` ("¿Cuándo y dónde?"); al cambiar de paso, el foco va al título y se
  anuncia "Paso 2 de 4".
- Cabañas libres como grupo de radios con `<label>`; las ocupadas son texto, no controles deshabilitados
  (VoiceOver las lee como información, no como botones que no funcionan).
- Contador de personas: botones con `aria-label` ("Quitar una persona" / "Agregar una persona") y valor en
  `aria-live="polite"`.
- Errores con `role="alert"` junto al campo (`aria-describedby`).

---

## 10. Diferencias con MASTER

1. **Sin barra inferior** (igual que Registrar pago): es un flujo con principio y fin.
2. **Formulario por pasos.** MASTER habla de "botón Guardar fijo abajo en formularios largos"; aquí el
   formulario largo se divide en 4 pasos, con "Siguiente" fijo abajo.

---

## 11. Datos y reglas (para el backend)

- `Huesped`: nombre, teléfono en E.164, email opcional. `Reserva`: unidad, llegada, salida, hora_estimada
  opcional, n_adultos, n_ninos, canal, monto_total (CLP entero > 0), notas (obligatoria si no hay abono; se valida en el servidor), `creado_por`. `Pago` (si hay abono):
  monto, medio, fecha = hoy, comprobante opcional; tipo **abono**.
- Validaciones en servidor y base de datos: `salida > llegada`, sin solape por unidad (`ExclusionConstraint`),
  capacidad según el tipo de unidad (§4.1), montos > 0. La capacidad depende de otra tabla (`Unidad`), así que no
  cabe en un `CheckConstraint` simple: se valida en el servicio dentro de la transacción **y** con un trigger de
  PostgreSQL (se decide al implementar).
- **Antes de crear:** consulta de disponibilidad en vivo a Beds24; creación en Beds24 y en la base local de forma
  que nunca quede una reserva "confirmada" sin bloqueo (estado "Bloqueo pendiente" + reintentos).
- Token único por intento de guardado (evita reservas duplicadas por doble toque o reintentos de red).
- Registro de auditoría al crear.
- Datos mínimos (Ley 21.719): solo nombre, teléfono y email opcional; nada más del huésped.

## 12. Pendientes

- ~~Capacidad por cabaña~~ → definida por tipo (§4.1, 29-10-2026).
- ~~Tipos~~ → Domos: Anko, Llu Llu · Cabañas chicas: Chañar, Cachi · Cabañas grandes: Antay, Liqui (29-10-2026).
- ~~Edad de niño~~ → **hasta 12 años** (29-10-2026).
- ~~Reserva sin abono~~ → **se permite como excepción**, con nota obligatoria que explica por qué y que la
  ficha muestra como aviso (29-10-2026; reemplaza al aviso ámbar).
- Fechas en **un calendario de dos toques** (llegada → salida) en vez de dos selectores nativos (29-10-2026).
- Nombre sin números ni símbolos (29-10-2026).
- Huésped que vuelve: si el teléfono ya existe, ofrecer "Este teléfono es de Pedro Rojas. ¿Usar sus datos?".
  Fuera de este diseño por ahora.
- ¿Guardar un borrador si se cierra la app a mitad de camino?
