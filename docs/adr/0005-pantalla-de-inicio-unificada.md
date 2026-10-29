# ADR 0005 — Pantalla de Inicio unificada

- **Estado:** Aceptada
- **Fecha:** 2026-10-29

## Contexto

El prototipo v0.1 separaba la operación diaria en dos pantallas: **Hoy** (llegadas, salidas, por cobrar, estado de unidades, turnos) y **Calendario** (semana por unidad). La consulta más frecuente es "quién llega, quién está y quién sale", lo que obligaba a saltar entre ambas.

Parte de los usuarios tiene alrededor de 60 años y no es técnica: cada pestaña extra es un lugar más donde perderse.

## Decisión

Una sola pantalla de entrada, **Inicio**: la agenda de hoy arriba y los **próximos 3 días** debajo, como lista por día (quién llega, quién sale y qué queda libre).

> **Actualización (29-10-2026):** la primera versión usaba un calendario semanal en grilla (días × cabañas). Al probar el prototipo resultó difícil de leer —obligaba a cruzar filas con columnas y a interpretar íconos con una leyenda—, así que se reemplazó por la lista de 3 días.

La barra inferior queda con 3 elementos: **Inicio** · **Nuevo** · **Dashboard**. "Nuevo" es un botón central que abre *Registrar pago* o *Nueva reserva*, para que ambas acciones estén a un toque desde cualquier pantalla.

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| Mantener Hoy y Calendario separados (prototipo) | Pantallas más cortas | Dos lugares para responder la misma pregunta; más navegación |
| Dashboard como pantalla de inicio | Vista del mes de un vistazo | Las cifras mensuales se consultan poco; la operación del día se consulta varias veces al día |
| 4 pestañas (Hoy, Calendario, Dashboard, Nuevo) | Patrón conocido | Más opciones en la barra; peor para usuarios no técnicos |

## Consecuencias

- ✅ La pregunta más frecuente se responde sin cambiar de pantalla.
- ✅ Registrar pago o reserva está siempre a un toque.
- ⚠️ Inicio es una pantalla larga: hay que priorizar el orden (lo urgente arriba) y mantener visible la barra inferior.
- ⚠️ Inicio solo cubre el corto plazo (3 días): la disponibilidad a más largo plazo necesitará otra vista, por definir.
- 📌 Actualiza `SPEC.md` §3 (módulos 1 y 2).
