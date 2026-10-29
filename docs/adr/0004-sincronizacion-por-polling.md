# ADR 0004 — Sincronización con Beds24 por polling

- **Estado:** Aceptada
- **Fecha:** 2026-10-23

## Contexto

La app no está expuesta a internet (ADR 0002). Los webhooks de Beds24 necesitan un endpoint HTTPS que Beds24 pueda alcanzar desde internet. Además, los webhooks de la API V2 se habilitan contactando al soporte de Beds24 y no son instantáneos: Beds24 indica un retraso promedio de alrededor de un minuto.

## Decisión

- **Entrada (Beds24 → app):** la app consulta la API V2 de Beds24 cada **2 minutos** (polling), pidiendo solo las reservas modificadas desde la consulta anterior. También consulta al abrir Inicio y, antes de guardar una reserva directa, confirma la disponibilidad en ese momento. Cada reserva se guarda con un *upsert* idempotente usando su ID de Beds24 (`id_externo`, campo único), así que traerla dos veces la actualiza en vez de duplicarla.
- **Salida (app → Beds24):** las reservas directas creadas en la app se envían a Beds24 al momento. Es una llamada saliente, así que no requiere exponer la app.

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| Webhooks de Beds24 | Cambios casi en tiempo real | Obliga a exponer un endpoint público (web pública o Tailscale Funnel), lo que contradice ADR 0002 |

## Consecuencias

- ✅ Cero superficie expuesta a internet.
- ✅ Fácil de probar con un cliente simulado de Beds24, sin llamar a la API real.
- ⚠️ Una reserva nueva de Airbnb o Booking puede tardar hasta ~2 minutos en aparecer en la app. **No aumenta el riesgo de overbooking**: Beds24 bloquea las fechas en las OTAs por su cuenta, sin depender de la app.
- ⚠️ Hay que respetar los límites de uso de la API de Beds24 (verificar en su documentación al implementar).
- ⚠️ Consultar cada 2 minutos gasta más del límite de uso de la API de Beds24: verificar ese límite al implementar y ajustar el intervalo si hace falta.
- 🔁 Revisar esta decisión si en el futuro se necesita información en tiempo real.

> **Actualización (29-10-2026):** el intervalo pasó de 10–15 minutos a 2 minutos, con consultas incrementales, para que lo que se ve en la app esté casi al día. Se descartaron de nuevo los webhooks para no exponer la app (ADR 0002).
