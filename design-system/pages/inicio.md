# Pantalla: Inicio

> Reglas específicas de esta pantalla. **Prevalecen** sobre `design-system/MASTER.md` solo en lo que se indica
> en la sección "Diferencias con MASTER"; en todo lo demás rige MASTER.

- **Estado:** borrador, pendiente de aprobación.
- **Decisión de origen:** ADR 0005 (Inicio unifica "Hoy" y "Calendario" del prototipo).
- **Pregunta que responde:** ¿quién llega, quién está, quién se va y qué falta cobrar, hoy y esta semana?

---

## 1. Estructura (de arriba hacia abajo)

Orden por urgencia: lo que exige una acción hoy va primero; la vista de la semana, después.

```
┌──────────────────────────────────────┐
│ Valle Petra   Actualizado hace 3 min (CL)│  1. Encabezado
│ Hoy                                   │
│ jueves 29 oct                         │
├──────────────────────────────────────┤
│ 3 de 5 ocupadas esta noche            │  2. Resumen
│ [ Llegan 2 ]  [ Salen 1 ]             │
├──────────────────────────────────────┤
│ Por cobrar hoy · $90.000              │  3. Por cobrar (solo si hay)
│ Cabaña Antay · Pedro Rojas            │
│ Falta pagar $90.000  [Registrar pago] │
├──────────────────────────────────────┤
│ Llegan hoy                            │  4. Hoy
│ ┌ Domo Anku · 15:00 ─────────────── › ┐│
│ │ María González · 2 personas        ││
│ │ (Airbnb) (✓ Pagado)                ││
│ │ [ Registrar llegada ]              ││
│ └────────────────────────────────────┘│
│ Salen hoy                             │
│ ┌ Cabaña Chañar · 12:00 ─────────── › ┐│
│ │ Familia Muñoz · (Directo) (✓ Pagado)││
│ │ [ Registrar salida ]               ││
│ └────────────────────────────────────┘│
├──────────────────────────────────────┤
│ Próximos 7 días      [‹ Antes][Después ›]│  5. Semana
│        Anku  Llu Llu Chañar Antay Cachi│
│ Hoy · jueves 29 oct                   │
│      [González][Soto ][ Aseo][Rojas][Libre]│
│ viernes 30 oct                        │
│      [González][Soto ][Fuentes][Rojas][Díaz]│
│ …                                     │
│ ✓ Pagado  ◌ Abonado  ! Sin pago  ✦ Aseo│
├──────────────────────────────────────┤
│ Turnos de hoy           + Agregar turno│  6. Turnos
│ Hot tub · Domo Llu Llu   20:00 – 21:30│
│ Quincho · Libre                       │
├──────────────────────────────────────┤
│   Inicio      ( + Nuevo )   Dashboard │  Barra inferior (MASTER)
└──────────────────────────────────────┘
```

**Primera pantalla visible** (≈ 700 px sobre la barra inferior en iPhone): debe mostrar encabezado, resumen,
por cobrar y al menos la primera llegada. La semana queda a un desplazamiento.

---

## 2. Secciones

### 1. Encabezado
- "Valle Petra" + estado de sincronización en texto secundario ("Actualizado hace 3 min").
  Tocarlo actualiza (área de 48 px); muestra "Actualizando…" mientras tanto.
- Botón de cuenta con iniciales (48 px, `aria-label="Cuenta"`).
- H1 "Hoy" (Fraunces) y debajo la fecha en formato de lista: `jueves 29 oct`.

### 2. Resumen
- Frase principal en 20px/700: "3 de 5 ocupadas esta noche".
- Dos contadores tocables: "Llegan 2" y "Salen 1"; al tocarlos desplazan a su lista.
- Sin gráficos ni porcentajes (eso es del Dashboard).

### 3. Por cobrar hoy (solo si hay)
- Una fila por reserva con saldo pendiente que llega o está alojada hoy.
- Texto: unidad · huésped, y "Falta pagar $90.000".
- Botón secundario **"Registrar pago"**: abre Registrar pago con todo prellenado
  (reserva, monto = saldo, fecha = hoy, medio = el último usado). → **2 toques** para registrar un saldo.
- Si no hay nada por cobrar, la sección no aparece (no se muestra "Nada por cobrar").

### 4. Hoy: llegadas y salidas
- Subtítulos "Llegan hoy" y "Salen hoy" (H2). Si no hay: una línea "No hay llegadas hoy".
- **Tarjeta de llegada:** unidad + hora estimada; huésped + nº de personas; chip de canal (neutro) y chip de pago
  (estado de MASTER). Una sola acción: **"Registrar llegada"**. Toda la tarjeta lleva a la ficha (›).
- **Tarjeta de salida:** igual, con acción **"Registrar salida"**.
- Tras registrar: la tarjeta cambia a chip "En curso" / "Terminada" y aparece el aviso
  "Llegada registrada · Deshacer" (8 s). Sin ventana de confirmación.
- Si una unidad con llegada hoy tiene aseo pendiente, la tarjeta lo dice en texto:
  "Aseo pendiente antes de la llegada".
- En la interfaz se dice **"llegada" / "salida"**; "check-in" / "check-out" quedan solo en el código y en la ficha.

### 5. Semana (calendario)
- **Filas = días, columnas = unidades.** Con 5 unidades, cada columna mide ~65 px en un iPhone de 375 px,
  sobre el mínimo de 48 px. (El prototipo usaba columnas = días: 7 columnas de ~37 px, bajo el mínimo.)
- Encabezado de columnas fijo al desplazar: "Anku", "Llu Llu", "Chañar", "Antay", "Cachi" (14px/700).
- Cada día lleva una línea de título a todo el ancho en formato de lista: `viernes 30 oct`;
  el de hoy dice `Hoy · jueves 29 oct`. Así no hace falta abreviar el día.
- **Celda ocupada** (mín. 64 px de alto): ícono del estado de pago arriba y apellido del huésped abajo
  (14px/600; si no cabe, se corta con "…" y el nombre completo va en el `aria-label`).
  Fondo según estado de pago (colores de MASTER): el color **acompaña** al ícono y al texto, no los reemplaza.
- **Celda con aseo pendiente:** ícono `sparkles` + "Aseo".
  Si ese mismo día llega alguien, la celda muestra la llegada y el aseo se avisa en la tarjeta de "Llegan hoy".
- **Celda libre:** texto "Libre" en gris. Tocarla abre **Nueva reserva con unidad y fecha de llegada prellenadas**.
- Tocar una celda ocupada abre la ficha de la reserva.
- Leyenda con texto bajo la grilla: "✓ Pagado · ◌ Abonado · ! Sin pago · ✦ Aseo" (íconos Lucide, no emoji).
- Navegación: botones "‹ Antes" y "Después ›" (48 px, con texto). Siempre parte en **hoy** (próximos 7 días,
  no lunes a domingo). Si se movió de semana, aparece "Volver a hoy".
- El canal **no** se muestra en la grilla (está en la tarjeta y en la ficha): la celda no da para más sin
  volverse ilegible.

### 6. Turnos de hoy
- Lista de hot tub y quincho: amenidad · unidad y horario (`20:00 – 21:30`), o "Libre".
- Acción de texto "+ Agregar turno".

---

## 3. Caminos cortos (conteo de toques)

| Tarea | Camino | Toques |
|---|---|---|
| Registrar el saldo de quien llega hoy | Por cobrar › "Registrar pago" → "Guardar pago" | **2** |
| Registrar una llegada | "Registrar llegada" | **1** (+ Deshacer) |
| Registrar una salida | "Registrar salida" | **1** (+ Deshacer) |
| Nueva reserva en un día libre | Celda "Libre" → completar huésped y monto → "Guardar" | 1 + formulario |
| Nueva reserva cualquiera | "Nuevo" → "Nueva reserva" → formulario | 2 + formulario |
| Registrar un pago cualquiera | "Nuevo" → "Registrar pago" → elegir reserva → "Guardar pago" | 4 |
| Ver una reserva | Tarjeta o celda | 1 |

---

## 4. Estados de la pantalla

| Estado | Qué se ve |
|---|---|
| Cargando | Esqueletos del tamaño real de resumen, tarjetas y grilla (sin saltos al cargar). |
| Sin llegadas / salidas | "No hay llegadas hoy" / "No hay salidas hoy" en una línea. |
| Semana sin reservas | Grilla con todas las celdas "Libre". |
| Sin conexión | Banda superior "Sin conexión. Los datos pueden no estar al día." Botones de registro deshabilitados con el texto "Sin conexión". |
| Beds24 sin actualizar > 30 min | Aviso ámbar bajo el encabezado: "No se pudo actualizar desde Beds24. Las reservas nuevas de Airbnb o Booking podrían no aparecer." |
| Error al registrar | Aviso rojo en la tarjeta: "No se pudo registrar la llegada. Intenta de nuevo." La tarjeta no cambia de estado. |

---

## 5. Accesibilidad

- La grilla es una `<table>`: unidades en `<th scope="col">`, días en `<th scope="row">` (o `rowgroup`).
- Cada celda es un enlace o botón con `aria-label` completo:
  "Cabaña Antay, viernes 30 oct: Pedro Rojas, abonado" / "Domo Anku, sábado 31 oct: libre, crear reserva".
- Los avisos de "Deshacer" y de error usan `role="status"` / `role="alert"`.
- Orden de foco = orden visual. Foco visible con `--color-focus`.
- Probado con el texto del iPhone en tamaño grande: las tarjetas crecen hacia abajo; la grilla mantiene
  5 columnas y corta apellidos antes de cortar encabezados.

---

## 6. Diferencias con MASTER

1. **Acciones de las tarjetas en estilo secundario.** MASTER pide una acción principal por pantalla; en Inicio
   hay varias tarjetas con acción. Para no llenar la pantalla de terracota, "Registrar llegada / salida / pago"
   usan el botón **secundario**; el único elemento terracota es "Nuevo" en la barra inferior.
2. **Celdas de la grilla a 64 px de alto y ~65 px de ancho** (MASTER pide ≥ 48 px: se cumple, se fija el valor).

---

## 7. Datos que necesita (para el backend)

- Unidades activas (orden fijo: Anku, Llu Llu, Chañar, Antay, Cachi).
- Reservas que se cruzan con [hoy, hoy + 7 días): unidad, huésped, n_huespedes, canal, llegada, salida,
  hora_estimada, estado, **estado de pago derivado** y **saldo** (calculados, nunca guardados).
- Tareas de aseo pendientes por unidad.
- Turnos de amenidades de hoy.
- Hora de la última sincronización exitosa con Beds24.

## 8. Pendientes

- Qué pasa cuando la sexta cabaña vuelva a estar activa (6 columnas ≈ 54 px: sigue sobre 48 px).
- Confirmar si el "último medio de pago usado" se recuerda por usuario o global.
