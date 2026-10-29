# ADR 0002 — Acceso privado con Tailscale

- **Estado:** Aceptada
- **Fecha:** 2026-10-23

## Contexto

La app la usan como máximo 4 personas de la familia, todas desde iPhone. Guarda datos personales de huéspedes (nombres, teléfonos, comprobantes de pago), y desde el 1 de diciembre de 2026 rige la Ley 21.719 de protección de datos personales.

No hay ninguna razón de negocio para que la app sea accesible desde internet.

## Decisión

La app **no se expone a internet**. Solo se accede a través de la tailnet de Tailscale (plan Personal).

El HTTPS lo entrega **Tailscale Serve**, que emite automáticamente un certificado TLS para el nombre `*.ts.net` del servidor. HTTPS es además requisito de la PWA (ver ADR 0003).

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| Web pública con login + 2FA | Acceso desde cualquier navegador; permite webhooks | Recibe escaneos de bots desde el primer día; exige parches, TLS, rate limiting y monitoreo constantes, sin beneficio real para 4 usuarios |
| Tailscale Funnel | Usa la misma herramienta | Funnel expone el servicio a internet: mismo problema que la web pública |

## Consecuencias

- ✅ La app es invisible desde internet: la superficie de ataque es mínima.
- ✅ Sin costo: el plan Personal es gratis hasta 6 usuarios con dispositivos ilimitados.
- ✅ Mantención mínima de la capa de acceso.
- ⚠️ Cada familiar debe instalar Tailscale en su iPhone y mantener la sesión iniciada.
- ⚠️ Beds24 no puede enviar webhooks a la app (ver ADR 0004).
- ⚠️ Si el mini PC o Tailscale fallan, nadie tiene acceso a la app hasta que se restablezcan.
- 📌 Para el portafolio se publica una **demo separada** con datos ficticios (ver SPEC, fase 5). Producción nunca se expone para mostrarla.
