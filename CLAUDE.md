# Valle Petra Gestión

App interna (PWA) para gestionar las reservas y la operación de Valle Petra.
Lee `SPEC.md` antes de cualquier cambio grande: ahí están las decisiones de arquitectura, el modelo de datos y las reglas de negocio.

## Stack

- Python 3.12+ con `uv`, Django, PostgreSQL, Docker Compose.
- Tests con `pytest`; lint y formato con `ruff`.
- Frontend: PWA servida por Django, pensada para iPhone.

## Comandos

Actualiza esta sección cuando exista el scaffold.

- Base de datos local: `docker compose up -d db`
- Tests: `uv run pytest`
- Lint: `uv run ruff check .`

## Reglas del proyecto

- Nombres de dominio en español (`Reserva`, `Pago`, `Unidad`, `Huesped`), igual que en el prototipo. Interfaz en español de Chile.
- Montos en CLP como enteros. Nunca float ni decimales.
- El saldo de una reserva siempre se calcula a partir de sus pagos; nunca se guarda.
- Nunca guardar números de tarjeta ni datos de cuentas bancarias.
- Nunca commitear secretos. Todo va en `.env`; mantener `.env.example` actualizado sin valores reales.
- Toda llamada a Beds24 pasa por `src/beds24/`, que debe tener un modo simulado. Los tests nunca llaman a la API real.
- Las validaciones críticas (sin solapes, montos > 0) se garantizan también en la base de datos, no solo en formularios.
- Cada cambio de modelo va con su migración y sus tests.
- Commits pequeños. Trabajo diario directo en `main`; ramas `feature/<nombre>` solo para cambios grandes o experimentales.

## Antes de actuar, pregunta si vas a

- Cambiar el esquema de un modelo que ya tiene datos o migraciones aplicadas.
- Agregar una dependencia nueva.
- Modificar algo en `deploy/` o en la configuración de seguridad.

## Documentación

Este proyecto también es de portafolio. Cuando tomes una decisión de diseño o de seguridad relevante, propón un ADR corto en `docs/adr/` (contexto → decisión → consecuencias).

## Diseño (UI/UX)

### Contexto
- Valle Petra: negocio de domos y cabañas turísticas. App INTERNA de gestión, no una web pública.
- Usuarios: máximo 4 personas de mi familia, todos desde iPhone.
- Acceso privado vía Tailscale; el servidor corre en un mini PC.
- Funciones: reservas, check-in/check-out, dashboard y registro manual de pagos
  (abono, método de pago, plataforma de origen: Airbnb, Booking o directo).
- Nunca se guardan ni se piden datos de tarjeta.
- Stack frontend: PWA servida por Django (plantillas del servidor), instalable desde Safari en iPhone. Librerías de CSS/JS: por definir.

### Estado actual
- Existe un prototipo v0.1 en docs/mockup/v0.1/ (6 pantallas, exportado desde claude.ai).
  Úsalo como referencia de pantallas, contenido y flujos, no como base visual:
  el diseño se rehace con la skill ui-ux-pro-max.
- No modifiques ni borres docs/mockup/v0.1/; queda como respaldo del original.

### Flujo de trabajo de diseño
1. Propón 2 o 3 direcciones visuales breves y espera que elija una.
2. Con la dirección elegida, genera design-system/MASTER.md y espera mi aprobación.
3. Diseña pantalla por pantalla, empezando por la más usada, aplicando el sistema de diseño.
4. Antes de agregar pantallas o flujos que no estén en SPEC.md, pregunta primero.

### Reglas de diseño
- Usa la skill ui-ux-pro-max para todo trabajo de UI.
- Mobile-first para iPhone; la vista de escritorio es secundaria.
- Áreas táctiles de mínimo 44×44 pt; acciones principales al alcance del pulgar.
- Acciones frecuentes en pocos toques: nueva reserva, check-in, check-out y registrar pago.
- Claridad antes que decoración: los usuarios no son técnicos.
- Estados de reserva indicados con color + texto, nunca solo color.
- Interfaz en español, fechas DD-MM-AAAA (en listas: `viernes 23 oct`), moneda CLP sin decimales ($45.000).
