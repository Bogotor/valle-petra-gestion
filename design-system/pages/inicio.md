# Pantalla: Inicio

> Reglas específicas de esta pantalla. **Prevalecen** sobre `design-system/MASTER.md` solo en lo que se indica
> en la sección "Diferencias con MASTER"; en todo lo demás rige MASTER.

- **Estado:** aprobado (v2, tras probar el prototipo v0.2).
- **Decisión de origen:** ADR 0005 (Inicio unifica "Hoy" y "Calendario" del prototipo).
- **Pregunta que responde:** ¿quién llega, quién está, quién se va y qué falta cobrar, hoy y los próximos 3 días?
- **Prototipo:** canvas "Valle Petra Gestión v0.2" en claude.ai, artboard "Inicio · A".

### Historial
- **v1:** calendario semanal en grilla (filas = días, columnas = cabañas).
- **v2:** se reemplaza la grilla por una lista "Próximos días" de 3 días. Al probar el prototipo, la grilla
  resultó difícil de entender: obligaba a cruzar fila con columna y a leer íconos con una leyenda. Se comparó
  con una vista "por cabaña" y se eligió la vista "por día", que se lee como una agenda.

---

## 1. Estructura (de arriba hacia abajo)

Orden por urgencia: lo de hoy primero; los próximos días, al final.

```
┌──────────────────────────────────────┐
│ Valle Petra                      (CL) │  1. Encabezado
│ ⟳ Actualizado hace 3 min              │
│ Hoy                                   │
│ jueves 29 oct                         │
├──────────────────────────────────────┤
│ 3 de 5 ocupadas esta noche            │  2. Resumen
│ [ Llegan 2 ]  [ Salen 1 ]             │
├──────────────────────────────────────┤
│ Por cobrar hoy · $90.000              │  3. Por cobrar (solo si hay)
│ Cabaña Antay · Pedro Rojas            │
│ (Falta pagar $90.000) [Registrar pago]│
├──────────────────────────────────────┤
│ Llegan hoy / Salen hoy                │  4. Hoy (tarjetas con una acción)
├──────────────────────────────────────┤
│ Turnos de hoy           + Agregar turno│  5. Turnos
├──────────────────────────────────────┤
│ Próximos días                         │  6. Próximos días (3)
│ ┌ Mañana · viernes 30 oct  5 de 5 ocupadas ┐
│ │ ⇥ Llega  Andrés Fuentes            › │
│ │          Cabaña Chañar               │
│ │ ⇥ Llega  Valentina Díaz            › │
│ │          Cabaña Cachi                │
│ │ Todas ocupadas esa noche             │
│ └──────────────────────────────────────┘
│ ┌ Pasado mañana · sábado 31 oct  3 de 5 ┐
│ │ ⇤ Sale   Camila Soto · Domo Llu Llu ›│
│ │ ⇤ Sale   Pedro Rojas · Cabaña Antay ›│
│ │ Libres esa noche:                    │
│ │ [Domo Llu Llu +] [Cabaña Antay +]    │
│ └──────────────────────────────────────┘
│ ┌ domingo 1 nov            3 de 5 ocupadas ┐
│ └ …                                    ┘
├──────────────────────────────────────┤
│   Inicio      ( + Nuevo )   Dashboard │  Barra inferior FIJA (MASTER)
└──────────────────────────────────────┘
```

- La **barra inferior queda fija**; solo se desplaza el contenido.
- **Primera pantalla visible** (≈ 750 px sobre la barra en iPhone): encabezado, resumen, por cobrar y al menos
  la primera llegada.

---

## 2. Secciones

### 1. Encabezado
- "Valle Petra" + estado de sincronización ("Actualizado hace 3 min"). Tocarlo actualiza (área de 48 px).
- Botón de cuenta con iniciales (48 px, `aria-label="Cuenta"`).
- H1 "Hoy" (Fraunces) y debajo la fecha en formato de lista: `jueves 29 oct`.

### 2. Resumen
- Frase principal en 20px/700: "3 de 5 ocupadas esta noche".
- Dos botones "Llegan 2" y "Salen 1" que desplazan a su lista. Sin gráficos (eso es del Dashboard).

### 3. Por cobrar hoy (solo si hay)
- Una fila por reserva con saldo pendiente que llega o está alojada hoy: unidad · huésped y "Falta pagar $90.000".
- Botón secundario **"Registrar pago"**: abre el pago con todo prellenado (monto = saldo, fecha = hoy,
  medio = Transferencia). → **2 toques** para registrar un saldo.
- Tras guardar: aviso "Pago de $90.000 guardado · Deshacer" y la sección desaparece.
- Si no hay nada por cobrar, la sección no aparece.

### 4. Hoy: llegadas y salidas
- Subtítulos "Llegan hoy" y "Salen hoy". Si no hay: una línea "No hay llegadas hoy".
- **Tarjeta:** unidad + hora; huésped + nº de personas; chip de canal (neutro) y chip de pago. El encabezado de la
  tarjeta lleva a la ficha (›). Una sola acción: **"Registrar llegada"** o **"Registrar salida"**.
- Tras registrar: chip "En curso" / "Terminada" y aviso "Llegada registrada · Deshacer" (8 s), sin confirmación.
  La salida avisa "Salida registrada. Aseo creado."
- Si una unidad con llegada hoy tiene aseo pendiente, la tarjeta lo dice: "Aseo pendiente antes de la llegada".
- En la interfaz se dice **"llegada" / "salida"**; "check-in" / "check-out" quedan solo en el código.

### 5. Turnos de hoy
- Hot tub y quincho: amenidad · unidad y horario (`20:00 – 21:30`), o "Libre". Acción "+ Agregar turno".

### 6. Próximos días
- **Por defecto, 3 días** después de hoy. Mostrar muchos días de entrada confunde: los demás se muestran
  **solo cuando alguien los pide**.
- Botón secundario **"Mostrar 7 días más"** al final de la lista: cada toque agrega 7 días (máximo 31 días en
  total; al llegar al máximo el botón desaparece).
- Cuando se están mostrando más de 3 días, aparece un botón de texto **"Mostrar menos"** que vuelve a 3 días.
- Al salir de Inicio y volver, la lista vuelve a 3 días.
- Una tarjeta por día. Título en formato de lista con etiqueta relativa para los dos primeros:
  `Mañana · viernes 30 oct`, `Pasado mañana · sábado 31 oct`, `domingo 1 nov`.
  A la derecha: "N de 5 ocupadas".
- **Filas de movimiento** (botones de 64 px, llevan a la ficha):
  - Verbo con ícono, en un ancho fijo: **Llega** (`log-in`) / **Sale** (`log-out`).
  - Nombre completo del huésped (17px/700) y la unidad debajo (15px).
  - En las llegadas, chip de pago **solo si falta pagar** ("Abonado · falta $60.000", "Sin pago · falta $150.000").
    Si está pagado no se muestra nada, para no recargar.
  - Orden: primero las llegadas, después las salidas.
- Día sin movimiento: "Sin llegadas ni salidas".
- **Libres esa noche:** botones con el nombre completo de cada unidad libre y un "+" (48 px, borde punteado).
  Tocarlo abre Nueva reserva con unidad y fecha de llegada prellenadas.
  Si no queda ninguna: "Todas ocupadas esa noche".
- Sin colores por canal ni leyenda: todo se lee como texto.

---

## 3. Caminos cortos (conteo de toques)

| Tarea | Camino | Toques |
|---|---|---|
| Registrar el saldo de quien llega hoy | Por cobrar › "Registrar pago" → "Guardar pago" | **2** |
| Registrar una llegada / salida | "Registrar llegada" / "Registrar salida" | **1** (+ Deshacer) |
| Nueva reserva en una noche libre de los próximos 3 días | Botón de unidad libre → formulario → "Guardar" | 1 + formulario |
| Nueva reserva cualquiera | "Nuevo" → "Nueva reserva" → formulario | 2 + formulario |
| Registrar un pago cualquiera | "Nuevo" → "Registrar pago" → elegir reserva → "Guardar pago" | 4 |
| Ver una reserva | Tarjeta o fila | 1 |

---

## 4. Estados de la pantalla

| Estado | Qué se ve |
|---|---|
| Cargando | Esqueletos del tamaño real de resumen, tarjetas y días (sin saltos al cargar). |
| Sin llegadas / salidas hoy | "No hay llegadas hoy" / "No hay salidas hoy" en una línea. |
| Día sin movimiento | "Sin llegadas ni salidas". |
| Sin conexión | Banda superior "Sin conexión. Los datos pueden no estar al día." Botones de registro deshabilitados con el texto "Sin conexión". |
| Beds24 sin actualizar > 30 min | Aviso ámbar bajo el encabezado: "No se pudo actualizar desde Beds24. Las reservas nuevas de Airbnb o Booking podrían no aparecer." |
| Error al registrar | Aviso rojo en la tarjeta: "No se pudo registrar la llegada. Intenta de nuevo." La tarjeta no cambia de estado. |

---

## 5. Accesibilidad

- Cada día es un `<article>` con su título como `<h3>`; las filas son `<a>` o `<button>` con texto completo
  (no necesitan `aria-label` extra: el verbo, el nombre y la unidad son texto visible).
- Los botones de unidad libre llevan `aria-label` completo: "Reservar Cabaña Antay desde el sábado 31 oct".
- Los avisos de "Deshacer" y de error usan `role="status"` / `role="alert"`.
- Orden de foco = orden visual. Foco visible con `--color-focus`.
- Con el texto del iPhone en tamaño grande, las filas crecen hacia abajo; nada se corta.

---

## 6. Diferencias con MASTER

1. **Acciones de las tarjetas en estilo secundario.** MASTER pide una acción principal por pantalla; en Inicio
   hay varias tarjetas con acción. Para no llenar la pantalla de terracota, "Registrar llegada / salida / pago"
   usan el botón **secundario**; el único elemento terracota es "Nuevo" en la barra inferior.

---

## 7. Datos que necesita (para el backend)

- Unidades activas (orden fijo: Anku, Llu Llu, Chañar, Antay, Cachi).
- Reservas que se cruzan con [hoy, hoy + 4 días) al cargar; al pedir más días, el tramo siguiente
  (hasta hoy + 32 días): unidad, huésped, n_huespedes, canal, llegada, salida,
  hora_estimada, estado, **estado de pago derivado** y **saldo** (calculados, nunca guardados).
- Tareas de aseo pendientes por unidad.
- Turnos de amenidades de hoy.
- Hora de la última sincronización exitosa con Beds24.

## 8. Pendientes

- "Mostrar 7 días más" cubre hasta un mes. Si hace falta buscar disponibilidad más lejos (por ejemplo, una
  vista "por cabaña" o un buscador de fechas), es una pantalla nueva: decidir si se agrega a SPEC.md.
