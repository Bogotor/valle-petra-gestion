# ADR 0006 — Dirección visual «Piedra y terracota»

- **Estado:** Aceptada
- **Fecha:** 2026-10-29

## Contexto

El prototipo v0.1 tenía una identidad cálida, pero una revisión de UI/UX encontró problemas de uso:

- Un mismo color significaba varias cosas: el terracota era a la vez acción principal, Airbnb, "ocupado" y "por cobrar"; en el calendario el canal se identificaba solo por color.
- Áreas táctiles bajo 44 px en el calendario y texto de 11–12 px.
- Tarjetas casi indistinguibles del fondo (contraste 1,10:1), problemático al sol.
- Campos de formulario de 15 px, que hacen zoom en iOS, y montos sin teclado numérico.

Los usuarios son 4 personas de la familia, en iPhone, incluidos usuarios de ~60 años no técnicos.

## Decisión

Adoptar la dirección **«Piedra y terracota»**, documentada en `design-system/MASTER.md`:

- Fondo piedra `#EFE9DF`, tarjetas blancas con borde visible; texto `#1C1917`.
- Terracota `#A23E22` **solo** para la acción principal. Los canales pasan a chips neutros con texto; el color se reserva para estados de pago y estadía, siempre con texto e ícono.
- Fraunces solo en títulos de pantalla y Manrope para todo lo demás, servidas desde el propio servidor.
- Áreas táctiles de 48 px, texto mínimo de 14 px y campos de 16 px.

Un modo oscuro basado en la dirección «Cielo del Elqui» queda como propuesta para después del MVP.

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| «iOS nativo» (fuente del sistema, gris y blanco) | La más simple de construir; familiar | Casi sin identidad propia; aporta menos al portafolio |
| «Cielo del Elqui» (oscuro primero) | Identidad fuerte; cómoda de noche | Se lee peor a pleno sol; como tema principal es arriesgado. Se conserva como modo oscuro futuro |
| Recomendación automática de la skill de diseño (vidrio líquido, azul y dorado) | — | Pensada para landing pages de marketing; el desenfoque y la transparencia perjudican contraste y rendimiento |

## Consecuencias

- ✅ Mantiene la identidad que la familia ya reconoce y corrige los problemas de contraste, tamaño y significado del color.
- ✅ Todos los pares de texto cumplen WCAG AA (contraste medido).
- ✅ Sin fuentes ni scripts de terceros en tiempo de ejecución.
- ⚠️ Más trabajo de diseño propio que la opción «iOS nativo».
- ⚠️ Los archivos de fuentes (licencia OFL) deben versionarse y servirse desde Django.
