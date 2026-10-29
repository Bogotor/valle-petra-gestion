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
- Commits pequeños y ramas `feature/<nombre>`.

## Antes de actuar, pregunta si vas a

- Cambiar el esquema de un modelo que ya tiene datos o migraciones aplicadas.
- Agregar una dependencia nueva.
- Modificar algo en `deploy/` o en la configuración de seguridad.

## Documentación

Este proyecto también es de portafolio. Cuando tomes una decisión de diseño o de seguridad relevante, propón un ADR corto en `docs/adr/` (contexto → decisión → consecuencias).
