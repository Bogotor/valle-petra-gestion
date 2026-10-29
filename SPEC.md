# Valle Petra Gestión — Especificación v0.2

App interna (PWA) para gestionar reservas, check-in/out, pagos y operaciones de Valle Petra.
También es un proyecto de portafolio (backend Python + seguridad).
El porqué de cada decisión, con las alternativas descartadas, está en `docs/adr/`.

## 1. Contexto

- Valle Petra: retreat boutique familiar en Vicuña, Valle del Elqui (Chile).
- Unidades activas: Domo Anku, Domo Llu Llu, Cabaña Chañar, Cabaña Antay, Cabaña Cachi (una sexta cabaña en renovación).
- Amenidades con turnos: hot tub y quincho.
- Usuarios: máximo 4 personas de la familia, todas con iPhone. Uso 100% interno, nunca público.
- Canales de reserva: Airbnb, Booking y reservas directas (WhatsApp, Instagram, teléfono, presencial).

## 2. Decisiones de arquitectura

| Decisión | Detalle |
|---|---|
| Enfoque híbrido | Beds24 (PMS) es la fuente de verdad del calendario y la conexión oficial con Airbnb/Booking. La app propia es la fuente de verdad de la operación (check-in/out, pagos manuales, aseo, turnos, dashboard). |
| Sincronización | Polling a la API V2 de Beds24 cada 10–15 min. Sin webhooks, porque la app no se expone a internet. Upsert idempotente por ID externo de Beds24. |
| Reservas directas | Se crean en la app y se envían a Beds24 vía API, para que las fechas se bloqueen en Airbnb y Booking. |
| Stack | Python 3.12+, Django, PostgreSQL, Docker Compose. Frontend como PWA servida por Django, instalable desde Safari. |
| Acceso | Solo vía Tailscale (plan Personal). HTTPS con Tailscale Serve (certificado `*.ts.net`). Nada expuesto a internet. |
| Entornos | Desarrollo en MacBook Pro (Apple Silicon/ARM). Producción en mini PC propio siempre encendido. |
| Permisos Beds24 | Solo los scopes de la API V2 estrictamente necesarios (mínimo privilegio). |

### Limitaciones conocidas

- Sin internet, o con el mini PC o Tailscale caídos, nadie puede usar la app hasta que se restablezcan. El calendario de las OTAs sigue protegido, porque Beds24 sincroniza los canales por su cuenta.
- Por el polling, una reserva nueva de Airbnb o Booking puede tardar hasta ~15 minutos en aparecer en la app (ADR 0004).

## 3. Módulos (MVP)

Referencia de pantallas, contenido y flujos: prototipo v0.1 en `docs/mockup/v0.1/`, exportado desde claude.ai. No es la base visual: el diseño se rehace según la sección Diseño de `CLAUDE.md`.

1. **Hoy**: ocupación de la noche, llegadas, salidas, monto por cobrar, estado de cada unidad, turnos del día.
2. **Calendario**: vista semanal por unidad, color por canal, marca de aseo pendiente.
3. **Ficha de reserva**: datos del huésped, fechas, estado de la estadía (confirmada → check-in → check-out), pagos, turnos, notas.
4. **Nueva reserva directa**: nombre, teléfono (+56 por defecto), email opcional, nº de huéspedes, unidad, llegada, salida, hora estimada, canal, monto total, abono (medio, monto, comprobante), notas. Verifica disponibilidad en vivo y bloquea el guardado si hay choque.
5. **Registrar pago**: tipo (abono/saldo), monto CLP, medio (transferencia, efectivo, tarjeta vía link de pago, payout de plataforma), fecha, comprobante opcional, nota.
6. **Dashboard mensual**: ocupación, ingreso neto por canal (descontando comisiones), ocupación por unidad, por cobrar, próximos 7 días.
7. **Operaciones**: tarea de aseo automática al registrar check-out; turnos de hot tub y quincho.

**Después del MVP:**

- **Notificaciones push** de la PWA, por ejemplo "llegada hoy" o "abono pendiente". En iOS 16.4+ solo funcionan con la app instalada en la pantalla de inicio (ADR 0003).

## 4. Modelo de datos inicial

- `Unidad`: nombre, tipo (domo/cabaña), capacidad, activa.
- `Huesped`: nombre, teléfono (E.164), email opcional.
- `Reserva`: id_externo (único, de Beds24), unidad (FK, PROTECT), huesped (FK), canal, llegada, salida, hora_estimada, n_huespedes, monto_total (CLP entero), estado, notas, version, creado_por, timestamps.
- `Pago`: reserva (FK, PROTECT), tipo, medio, monto (CLP entero), fecha, comprobante (opcional), registrado_por, nota.
- `TurnoAmenidad`: amenidad (hot tub/quincho), reserva (FK opcional), inicio, fin.
- `TareaAseo`: unidad, reserva, estado, completada_por, completada_en.
- `RegistroAuditoria`: usuario, acción, objeto, cambios, fecha.

## 5. Reglas de negocio y validaciones

- `salida > llegada`.
- Sin reservas solapadas en la misma unidad, garantizado en la base de datos (`ExclusionConstraint` de PostgreSQL sobre el rango de fechas + unidad), no solo en el formulario.
- Montos en CLP como enteros, siempre > 0. Nunca float.
- Saldo = total − suma de pagos. Se calcula, nunca se guarda a mano.
- Estado de pago derivado: pendiente / abonado / pagado. Alertar si los pagos superan el total.
- Check-out solo después de check-in. El check-out genera una tarea de aseo.
- Turnos sin solape por amenidad y dentro de las fechas de la reserva.
- Bloqueo optimista con campo `version` para evitar que dos usuarios se pisen cambios.
- `n_huespedes` entre 1 y la capacidad de la unidad.
- En reservas de Airbnb/Booking, los campos sincronizados (fechas, canal, monto) se editan en Beds24, no en la app.

## 6. Seguridad y privacidad

- Acceso solo por Tailscale. Autenticación de Django + 2FA (TOTP). Roles: administrador y operador.
- No se guardan números de tarjeta ni datos de cuentas bancarias. Los pagos son registros manuales.
- Comprobantes en almacenamiento privado, servidos solo a usuarios autenticados.
- Secretos solo en `.env` (fuera de git), con `.env.example` versionado. `gitleaks` en CI.
- Registro de auditoría de cambios. Logs estructurados enviados a Wazuh (homelab).
- Backups diarios de PostgreSQL cifrados y guardados fuera del mini PC. Prueba de restauración mensual.
- Ley 21.719 de protección de datos (vigencia 1 de diciembre de 2026): minimización de datos, retención definida, atención de solicitudes de acceso y eliminación.

## 7. Entornos

- **Desarrollo (MacBook)**: Docker Compose con PostgreSQL, datos ficticios (seeds), cliente de Beds24 en modo simulado con fixtures. Los tests nunca llaman a la API real.
- **Producción (mini PC)**: Docker Compose, imágenes construidas en el propio mini PC (evita problemas ARM vs x86).
- **GitHub**: repo público (portafolio): sin secretos, datos reales ni detalles de infraestructura. CI con pytest, ruff y gitleaks en cada push.

## 8. Fases

0. Spec, repo y `CLAUDE.md`.
1. Modelos, validaciones, admin y tests (sin Beds24).
2. Pantallas de la PWA según el prototipo.
3. Beds24: lectura por polling; después, creación de reservas directas.
4. Despliegue en mini PC: Docker, Tailscale Serve, backups, logs a Wazuh.
5. Portafolio: README, ADRs y demo pública separada de producción: otro servidor y otra base de datos, sin secretos compartidos y con datos ficticios que se resetean cada noche. Sus logs van a Wazuh para documentar detecciones reales de escaneos y ataques.

## 9. Pendientes por definir

- Sistema operativo y procesador del mini PC.
- Capacidad máxima por unidad.
- Si las tarifas y temporadas se manejan en la app o solo en Beds24.
- Comisiones reales por canal, para calcular el ingreso neto.
- Política de retención de datos de huéspedes.
