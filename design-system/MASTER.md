# Sistema de diseño — Valle Petra Gestión

> **Lógica:** al diseñar una pantalla, revisa primero `design-system/pages/<pantalla>.md`.
> Si existe, sus reglas **prevalecen** sobre este archivo. Si no, sigue estrictamente las reglas de abajo.

- **Dirección:** B «Piedra y terracota». Modo oscuro futuro basado en C «Cielo del Elqui» (ver al final).
- **Versión:** 0.1 (borrador, pendiente de aprobación).
- **Usuarios:** 4 personas de la familia, en iPhone, incluidos usuarios de ~60 años no técnicos.
  Si una decisión obliga a elegir entre "más simple" y "más completo", gana "más simple".

---

## 1. Principios

1. **Una acción principal por pantalla**, siempre visible y al alcance del pulgar (parte inferior).
2. **Pocos toques:** registrar un pago o una reserva parte desde cualquier pantalla y usa valores por defecto
   cuando ayudan (fecha = hoy, medio = Transferencia). **Los montos nunca vienen prellenados**: los escribe la
   persona, para que no se guarde una cifra sin haberla mirado.
3. **Nada escondido:** sin menú hamburguesa, sin acciones solo con deslizar o mantener presionado.
   Todo ícono va acompañado de texto.
4. **Lenguaje simple:** "Pagado", "Falta pagar $90.000", "Llega hoy". Nunca jerga técnica
   (OTA, sync, payout, upsert).
5. **Color + texto, nunca solo color.** Cada estado se escribe; el color solo refuerza.
6. **Perdonar errores:** después de guardar, aviso con opción "Deshacer" en vez de pedir confirmación.
   Confirmación explícita solo para acciones destructivas (anular reserva, borrar pago).
7. **Claridad antes que decoración:** sin degradados, vidrio, desenfoques ni ilustraciones de relleno.

---

## 2. Color (modo claro)

Todos los pares de texto cumplen WCAG AA (≥ 4,5:1); valores medidos.

### Base

| Rol | Hex | Variable CSS | Contraste |
|---|---|---|---|
| Fondo (piedra) | `#EFE9DF` | `--color-bg` | — |
| Superficie (tarjetas, hojas) | `#FFFFFF` | `--color-surface` | — |
| Borde de tarjeta | `#B3A797` | `--color-border` | 1,96:1 sobre fondo (separación visible) |
| Borde de campo de formulario | `#8C8379` | `--color-border-input` | 3,72:1 sobre blanco (WCAG 1.4.11) |
| Separador interno | `#E7E1D7` | `--color-divider` | decorativo |
| Texto principal | `#1C1917` | `--color-text` | 14,5:1 sobre fondo |
| Texto secundario | `#57534E` | `--color-text-muted` | 6,3:1 sobre fondo |
| Primario (terracota) | `#A23E22` | `--color-primary` | blanco encima 6,5:1 |
| Primario presionado | `#86321B` | `--color-primary-pressed` | blanco encima 8,5:1 |
| Texto sobre primario | `#FFFFFF` | `--color-on-primary` | — |
| Foco | `#1E4FA8` | `--color-focus` | 6,8:1 sobre fondo |
| Error | `#B42318` sobre `#FDECEA` | `--color-error`, `--color-error-bg` | 5,8:1 |

El **terracota es solo para la acción principal** (botón "Guardar", botón "Nuevo") y la pestaña activa.
No se usa para canales, estados ni montos.

### Estados (chips: texto + ícono + color)

| Estado | Texto | Fondo | Ícono (Lucide) | Contraste |
|---|---|---|---|---|
| **Pago:** Pagado | `#1F6B45` | `#E3F1E8` | `check-circle` | 5,6:1 |
| **Pago:** Abonado (falta saldo) | `#8A4B00` | `#FBEBD3` | `circle-dashed` | 5,8:1 |
| **Pago:** Sin pago | `#9F1D1D` | `#FBE4E2` | `alert-circle` | 6,5:1 |
| **Estadía:** Confirmada | `#1E4FA8` | `#E4ECFA` | `calendar-check` | 6,5:1 |
| **Estadía:** En curso (check-in hecho) | `#5B3A8C` | `#EEE7F7` | `log-in` | 7,2:1 |
| **Estadía:** Terminada (check-out) | `#57534E` | `#EFEBE4` | `log-out` | 6,4:1 |
| **Aseo:** Pendiente | `#1E4FA8` | `#E4ECFA` | `sparkles` | 6,5:1 |

Variables: `--state-<nombre>-fg` y `--state-<nombre>-bg` (p. ej. `--state-pagado-fg`).

### Canales

Los canales **no tienen color propio**: chip neutro con el nombre escrito ("Airbnb", "Booking", "Directo").
Fondo `#EFEBE4`, texto `#44403C` (8,6:1). Así el color queda libre para lo que importa: el estado.

---

## 3. Tipografía

| Uso | Fuente | Tamaño / peso | Interlineado |
|---|---|---|---|
| Título de pantalla (solo H1) | Fraunces | 30px / 600 | 1,15 |
| Título de sección (H2) | Manrope | 20px / 700 | 1,3 |
| Nombre de unidad o huésped en tarjeta | Manrope | 18px / 700 | 1,3 |
| Texto principal | Manrope | 17px / 500 | 1,45 |
| Texto secundario | Manrope | 15px / 500 | 1,45 |
| Etiquetas, chips, pestañas | Manrope | 14px / 700 | 1,3 |
| Monto destacado | Manrope | 28px / 700, `tabular-nums` | 1,15 |
| Campos de formulario | Manrope | **mínimo 16px** (evita el zoom de iOS) | — |

- **Nunca menos de 14px.** (El prototipo usaba 11–12px.)
- Montos y horas siempre con `font-variant-numeric: tabular-nums`.
- Fuentes **servidas desde el propio servidor** (archivos `.woff2`, licencia OFL), no desde Google Fonts:
  la app funciona sin depender de terceros y no filtra IPs de uso.
- Escalar en `rem` desde `html { font: -apple-system-body; }` para respetar el tamaño de texto
  que cada persona configuró en su iPhone (Ajustes › Pantalla › Tamaño del texto).

---

## 4. Formatos

| Dato | Formato | Ejemplo |
|---|---|---|
| Fecha (general: fichas, formularios, registros de pago) | DD-MM-AAAA | `23-10-2026` |
| Rango de fechas (general) | | `23-10-2026 → 25-10-2026` |
| Fecha **en listas** (agenda, calendario, próximas llegadas) | día de la semana completo + día + mes abreviado | `viernes 23 oct` |
| Rango **en listas** | | `viernes 23 → domingo 25 oct` |
| Hora | 24 h | `17:00` |
| Monto | CLP, punto de miles, sin decimales | `$45.000` |
| Teléfono | | `+56 9 1234 5678` |

- En listas se usa el día de la semana completo para que se lea de un vistazo qué día cae cada llegada.
  Si la fecha no es del año en curso, en listas también se usa DD-MM-AAAA.
- Generar con un formateador central (filtro de plantilla de Django), nunca concatenando strings a mano.
- En formularios se usan los selectores nativos de iOS (`type="date"`, `type="time"`); el formato
  anterior es para mostrar.

---

## 5. Espaciado, forma y profundidad

| Token | Valor | Uso |
|---|---|---|
| `--space-xs` | 4px | Ícono–texto |
| `--space-sm` | 8px | Separación mínima entre elementos tocables |
| `--space-md` | 16px | Padding de tarjeta, margen lateral de pantalla |
| `--space-lg` | 24px | Entre secciones |
| `--space-xl` | 32px | Antes de la acción principal |

| Token | Valor | Uso |
|---|---|---|
| `--radius-sm` | 8px | Campos |
| `--radius-md` | 12px | Botones, tarjetas |
| `--radius-full` | 999px | Chips |
| `--shadow-sm` | `0 1px 2px rgba(28,25,23,.08)` | Tarjetas (junto al borde) |
| `--shadow-lg` | `0 -4px 16px rgba(28,25,23,.12)` | Barra inferior y hojas (sheets) |

Sin otras sombras. Sin `backdrop-filter`.

---

## 6. Componentes

### Áreas táctiles
- **Mínimo 48×48px** (más que el mínimo de 44 de Apple, pensando en usuarios de 60+).
- Botón de acción principal: **56px de alto**, ancho completo.
- Al menos 8px entre dos elementos tocables.

### Botones
| Tipo | Estilo | Uso |
|---|---|---|
| Principal | fondo `--color-primary`, texto blanco 17px/700 | Uno por pantalla: "Guardar pago", "Registrar check-in" |
| Secundario | fondo blanco, borde `--color-border-input`, texto `--color-text` | "Ver ficha", "Cancelar" |
| Texto | sin fondo, texto `--color-primary` 17px/700, área de 48px | "+ Agregar turno" |
| Destructivo | fondo blanco, borde y texto `--color-error` | "Anular reserva" (siempre con confirmación) |

- Mientras se guarda: botón deshabilitado + texto "Guardando…". Después: aviso "Pago guardado · Deshacer".
- `:active` oscurece el fondo; sin animaciones de escala.

### Navegación
- **Barra inferior fija** con 3 elementos, cada uno con ícono **y** texto:
  **Inicio** · **Nuevo** · **Dashboard**.
  - **Inicio** (pantalla de entrada): agenda de hoy arriba (llegadas, salidas, por cobrar, turnos)
    y los próximos 3 días debajo. Reemplaza a "Hoy" y "Calendario" del prototipo.
  - **Nuevo** (botón central, terracota): abre una hoja con dos opciones grandes:
    **Nueva reserva** (primero) y **Registrar pago** (debajo).
  - **Dashboard**: cifras del mes.
- La pestaña activa: texto e ícono en `--color-primary` + peso 700 (no solo color).
- Profundidad máxima: 2 niveles (lista → ficha). Botón volver arriba a la izquierda **con texto**
  ("‹ Inicio").
- El detalle de cada pantalla va en `pages/<pantalla>.md`.

### Formularios
- Etiqueta visible siempre encima del campo; el placeholder solo es un ejemplo, nunca la etiqueta.
- Montos: `inputmode="numeric"`, el `$` fuera del campo y puntos de miles automáticos al escribir.
- Opciones con pocas alternativas (medio de pago, canal, unidad): botones grandes seleccionables,
  no listas desplegables.
- Valores por defecto inteligentes; los campos opcionales van al final y lo dicen: "Nota (opcional)".
- Errores en texto simple bajo el campo, diciendo cómo arreglarlo:
  "La salida debe ser después de la llegada".
- Botón "Guardar" fijo abajo en formularios largos.

### Tarjetas
- Fondo `--color-surface`, borde 1px `--color-border`, `--shadow-sm`, radio 12px, padding 16px.
- Toda la tarjeta es tocable si lleva a una ficha (con área de 48px o más), con un `›` a la derecha.
- Sin borde izquierdo de color como indicador de estado: el estado va en un chip.

### Avisos y estados del sistema
- **Sin conexión / servidor caído:** banda superior gris oscuro con texto "Sin conexión. Los datos pueden
  no estar al día." Nunca se pierde lo que se estaba escribiendo.
- **Sincronización Beds24:** "Actualizado hace 3 min" en texto secundario; si falla más de 30 min,
  aviso ámbar "No se pudo actualizar desde Beds24".
- **Vacío:** frase simple + acción ("No hay llegadas hoy").
- **Cargando:** esqueletos del tamaño real del contenido (sin saltos de diseño).

### Íconos
- Lucide, trazo 2px, 24px, siempre con texto al lado. SVG en línea (sin emoji).
  *(Copiar SVG en línea no agrega dependencia; si se quiere el paquete, preguntar antes.)*

### Movimiento
- 150–250 ms, solo `opacity` y `transform`. Respetar `prefers-reduced-motion`.

---

## 7. Anti-patrones (no usar)

- Información solo por color (p. ej. canal identificado solo por color en el calendario).
- Texto bajo 14px o campos de formulario bajo 16px.
- Áreas táctiles bajo 48px o tocables pegados entre sí.
- Acciones solo por gesto (deslizar, mantener presionado) o escondidas en menús.
- Vidrio, desenfoque, degradados, emoji como íconos, tarjetas con borde izquierdo de color.
- Fuentes o scripts cargados desde CDN de terceros.
- Ventanas de confirmación para acciones reversibles (usar "Deshacer").
- Jerga técnica en la interfaz.

---

## 8. Modo oscuro (propuesta, no implementar todavía)

Basado en la dirección C «Cielo del Elqui». Se activa con `prefers-color-scheme: dark`, con opción manual
en el menú de cuenta. Mismas variables CSS, distintos valores:

| Variable | Valor | Contraste |
|---|---|---|
| `--color-bg` | `#0B1020` | — |
| `--color-surface` | `#151B2E` | — |
| `--color-border-input` | `#5A6688` | 3,0:1 sobre superficie |
| `--color-text` | `#EEF0F6` | 16,6:1 |
| `--color-text-muted` | `#A3ABC2` | 8,3:1 |
| `--color-primary` | `#F2A541` (ámbar), texto encima `#0B1020` | 9,2:1 |
| Pagado | `#6EE7A8` sobre `#1B3A2C` | 8,1:1 |
| Abonado | `#F5C77E` sobre `#3A2A10` | 8,8:1 |
| Sin pago | `#FF9B91` sobre `#3D1A1A` | 7,6:1 |
| Confirmada / Aseo | `#8AB4FF` sobre `#1A2747` | 7,1:1 |
| En curso | `#C9B3F5` sobre `#2A2140` | 8,1:1 |

En modo oscuro el primario pasa de terracota a ámbar: el terracota sobre azul noche no alcanza contraste
suficiente para texto.

---

## 9. Checklist antes de entregar una pantalla

- [ ] Una sola acción principal, abajo y visible sin desplazar.
- [ ] Registrar pago / reserva alcanzable desde la barra inferior.
- [ ] Todo estado tiene texto (no solo color).
- [ ] Ningún texto bajo 14px; campos ≥ 16px; montos con `inputmode="numeric"`.
- [ ] Áreas táctiles ≥ 48px con ≥ 8px de separación.
- [ ] Fechas `23-10-2026` (en listas `viernes 23 oct`), montos `$45.000`, horas `17:00`.
- [ ] Estados vacío, cargando, error y sin conexión diseñados.
- [ ] Contraste AA verificado en los colores nuevos.
- [ ] Sin recursos de terceros (fuentes, íconos, scripts).
- [ ] Probado a 375px y 430px de ancho y con el texto del iPhone en tamaño grande.
