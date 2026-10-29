# ADR 0001 — PMS híbrido con Beds24

- **Estado:** Aceptada
- **Fecha:** 2026-10-23

## Contexto

Valle Petra tiene 5 unidades que se venden por Airbnb, Booking y reservas directas (WhatsApp, Instagram, teléfono, presencial). El riesgo operativo principal es el **overbooking**: que una misma unidad se reserve dos veces para las mismas fechas en canales distintos.

- Airbnb no ofrece API pública: solo da acceso a software partners aprobados.
- La alternativa gratuita es iCal: cada plataforma exporta e importa un link de calendario. Pero iCal solo sincroniza fechas (no precios ni mensajes) y puede tardar en actualizarse, lo que deja una ventana para dobles reservas.

## Decisión

Usar **Beds24** como PMS y channel manager, como fuente de verdad del calendario y de la conexión con Airbnb y Booking.

La app propia es la fuente de verdad de la **operación**: check-in/out, pagos manuales, aseo, turnos de hot tub y quincho, y dashboard. Se conecta a Beds24 por su API V2.

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| App propia solo con iCal | Gratis, control total | Riesgo de overbooking por la latencia de iCal; mantener la sincronización queda 100% a nuestro cargo |
| Solo un PMS, sin app propia | Operativo en días | No cubre turnos de amenidades ni el registro de pagos a nuestra manera; no aporta al portafolio |
| Smoobu | Interfaz más fácil | Cobra por unidad: ~€80/mes para 5 unidades según comparadores de terceros; la API requiere plan pagado |
| Lodgify | Producto completo | El acceso a la API depende del plan; el plan Starter no la incluye, según análisis de terceros |

Beds24 se eligió por:
- **Costo:** el más bajo de los evaluados. Base cercana a €15,90/mes más cargos pequeños por unidad y por canal, según comparadores de terceros (verificar en la web oficial antes de contratar).
- **Mínimo privilegio:** la API V2 separa permisos por alcance (`bookings`, `bookings-personal`, `bookings-financial`), así que la app puede pedir solo lo que necesita.

## Consecuencias

- ✅ Sincronización real con las OTAs sin depender de iCal.
- ✅ La app accede a Beds24 con el mínimo de permisos necesario.
- ⚠️ Costo mensual fijo y dependencia de un proveedor: si Beds24 cambia su API o sus precios, hay que adaptarse.
- ⚠️ Interfaz menos moderna y más configuración inicial que Smoobu.
- ⚠️ En reservas de Airbnb/Booking, los campos sincronizados (fechas, canal, monto) se editan en Beds24, no en la app.
