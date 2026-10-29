# ADR 0003 — PWA en vez de app nativa

- **Estado:** Aceptada
- **Fecha:** 2026-10-23

## Contexto

Los 4 usuarios usan iPhone. Es una app interna que no se publicará en la App Store. El desarrollo lo hace una sola persona, con foco en backend Python.

## Decisión

Construir una **PWA servida por Django**. Se instala desde Safari con "Agregar a pantalla de inicio" y se abre con su propio ícono, a pantalla completa.

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| App nativa iOS (Swift/SwiftUI) | Mejor integración con el sistema y notificaciones más confiables | Un segundo código (app nativa + API); distribuirla por TestFlight requiere la membresía Apple Developer Program (USD 99/año), y las builds de TestFlight vencen a los 90 días |

## Consecuencias

- ✅ Un solo código en Django para backend y frontend.
- ✅ Sin App Store, sin membresía de Apple y sin builds que vencen.
- ✅ Notificaciones push posibles: iOS 16.4+ soporta Web Push para apps web instaladas en la pantalla de inicio.
- ⚠️ Las push solo funcionan con la PWA instalada, no en una pestaña de Safari, y hay reportes de fiabilidad irregular.
- ⚠️ Requiere HTTPS (resuelto en ADR 0002).
- ⚠️ Menos acceso a funciones del sistema que una app nativa.
