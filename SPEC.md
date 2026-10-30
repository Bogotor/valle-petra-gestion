# Valle Petra Gestión — Especificación v0.2

App interna (PWA) para gestionar reservas, check-in/out, pagos y operaciones de Valle Petra.
También es un proyecto de portafolio (backend Python + seguridad).
El porqué de cada decisión, con las alternativas descartadas, está en `docs/adr/`.

## 1. Contexto

- Valle Petra: retreat boutique familiar en Vicuña, Valle del Elqui (Chile).
- Unidades activas (6): Domo Anko y Domo Llu Llu (domos), Cabaña Chañar y Cabaña Cachi (cabañas chicas), Cabaña Antay y Cabaña Liqui (cabañas grandes). Liqui volvió a estar activa el 29-10-2026.
- Servicios con turno, solo para huéspedes: **quincho** (sin costo) y **2 tinajas** (con costo, que se suma a la reserva). No hay hot tub.
- Usuarios: máximo 4 personas de la familia, todas con iPhone. Uso 100% interno, nunca público.
- Canales de reserva: Airbnb, Booking y reservas directas (WhatsApp, Instagram, teléfono, presencial).

## 2. Decisiones de arquitectura

| Decisión | Detalle |
|---|---|
| Enfoque híbrido | Beds24 (PMS) es la fuente de verdad del calendario y la conexión oficial con Airbnb/Booking. La app propia es la fuente de verdad de la operación (check-in/out, pagos manuales, aseo, turnos, dashboard). |
| Sincronización | Polling a la API V2 de Beds24 cada 2 min, pidiendo solo los cambios desde la última consulta; además al abrir Inicio y antes de guardar una reserva. Sin webhooks, porque la app no se expone a internet. Upsert idempotente por ID externo de Beds24. |
| Reservas directas | Se crean en la app y se envían a Beds24 vía API, para que las fechas se bloqueen en Airbnb y Booking. |
| Stack | Python 3.12+, Django, PostgreSQL, Docker Compose. Frontend como PWA servida por Django, instalable desde Safari. |
| Acceso | Solo vía Tailscale (plan Personal). HTTPS con Tailscale Serve (certificado `*.ts.net`). Nada expuesto a internet. |
| Entornos | Desarrollo en MacBook Pro (Apple Silicon/ARM). Producción en mini PC propio siempre encendido. |
| Permisos Beds24 | Solo los scopes de la API V2 estrictamente necesarios (mínimo privilegio). |

### Limitaciones conocidas

- Sin internet, o con el mini PC o Tailscale caídos, nadie puede usar la app hasta que se restablezcan. El calendario de las OTAs sigue protegido, porque Beds24 sincroniza los canales por su cuenta.
- Por el polling, una reserva nueva de Airbnb o Booking puede tardar hasta ~2 minutos en aparecer en la app (ADR 0004). No genera reservas duplicadas: antes de guardar una reserva directa se confirma la disponibilidad con Beds24 en ese momento.

## 3. Módulos (MVP)

Referencia de pantallas, contenido y flujos: prototipo v0.1 en `docs/mockup/v0.1/`, exportado desde claude.ai. No es la base visual: el diseño se rehace según la sección Diseño de `CLAUDE.md`.

1. **Inicio** (pantalla de entrada; une "Hoy" y "Calendario" del prototipo):
   - Agenda de hoy: ocupación de la noche, llegadas, salidas, monto por cobrar, estado de cada unidad, servicios del día (quincho y tinajas).
   - Próximos 3 días: por cada día, quién llega, quién sale, qué falta cobrar y qué unidades quedan libres.
2. *(Integrado en Inicio.)*
3. **Ficha de reserva**: datos del huésped, fechas, estado de la estadía (confirmada → check-in → check-out), pagos, servicios (quincho y tinajas), notas. Permite **cancelar** una reserva directa confirmada: pide confirmación y motivo, y libera las fechas en Beds24.
4. **Nueva reserva directa**: nombre, teléfono (+56 por defecto), email opcional, nº de huéspedes, unidad, llegada, salida, hora estimada, canal, monto total, abono (medio, monto, comprobante), notas. Verifica disponibilidad en vivo y bloquea el guardado si hay choque.
5. **Registrar pago**: tipo (abono/saldo, derivado del monto), monto CLP, medio (transferencia, efectivo, Webpay, Mercado Pago; Webpay y Mercado Pago son links de pago con tarjeta), fecha, comprobante opcional (foto o PDF, máx. 10 MB), nota. Los pagos de Airbnb y Booking llegan sincronizados desde Beds24; no se registran a mano salvo excepciones.
6. **Dashboard mensual**: ingresos del mes (estadías por fecha de llegada, menos comisiones, más tinajas), ocupación, ingreso neto por canal, ocupación por unidad, por cobrar, servicios del mes, próximos 7 días.
7. **Operaciones**: tarea de aseo automática al registrar check-out; servicios con turno (quincho y tinajas) a hora libre, sin solapes por recurso. La tinaja se cobra y su precio se suma a lo que falta pagar de la reserva.
8. **Exportar planilla del mes** (desde el Dashboard): un archivo **Excel (`.xlsx`)** con hojas de resumen, reservas, pagos y servicios del mes, para la contabilidad. Solo los datos necesarios (sin teléfonos ni emails de huéspedes). Textos escritos siempre como texto, nunca como fórmula (inyección de fórmulas). Cada descarga queda en la auditoría. Dependencia prevista: `openpyxl` (se confirma al implementar).

**Después del MVP:**

- **Notificaciones push** de la PWA, por ejemplo "llegada hoy" o "abono pendiente". En iOS 16.4+ solo funcionan con la app instalada en la pantalla de inicio (ADR 0003).

## 4. Modelo de datos inicial

- `Unidad`: nombre, tipo (`domo` / `cabana_chica` / `cabana_grande`), activa. La capacidad depende del tipo:
  - Domo y cabaña chica: hasta 3 adultos sin niños, o hasta 2 adultos con hasta 2 niños.
  - Cabaña grande: hasta 5 personas en total, en cualquier combinación.
  - Siempre al menos 1 adulto. Niño = hasta 12 años.
- `Huesped`: nombre, teléfono (E.164), email opcional.
- `Reserva`: id_externo (único, de Beds24), unidad (FK, PROTECT), huesped (FK), canal, llegada, salida, hora_estimada, n_adultos, n_ninos, monto_total (CLP entero), estado (confirmada / en_curso / terminada / cancelada), motivo_cancelacion, notas, version, creado_por, timestamps.
- `Pago`: reserva (FK, PROTECT), tipo, medio, monto (CLP entero), fecha, comprobante (opcional), registrado_por, nota.
- `Servicio`: nombre (Quincho, Tinaja 1, Tinaja 2), tipo (`quincho` / `tinaja`), se_cobra, activo.
- `TurnoServicio`: servicio (FK, PROTECT), reserva (FK, obligatoria), inicio, fin, monto (CLP entero; > 0 si se cobra, 0 si no), creado_por.
- `TareaAseo`: unidad, reserva, estado, completada_por, completada_en.
- `RegistroAuditoria`: usuario, acción, objeto, cambios, fecha.

## 5. Reglas de negocio y validaciones

- `salida > llegada`.
- Sin reservas solapadas en la misma unidad, garantizado en la base de datos (`ExclusionConstraint` de PostgreSQL sobre el rango de fechas + unidad), no solo en el formulario.
- Montos en CLP como enteros, siempre > 0. Nunca float.
- Saldo (lo que falta pagar) = monto de la estadía + suma de los servicios cobrados − suma de pagos. Se calcula, nunca se guarda a mano.
- Estado de pago derivado: pendiente / abonado / pagado. Alertar si los pagos superan el total.
- Check-out solo después de check-in. El check-out genera una tarea de aseo.
- Turnos de servicio sin solape por recurso (`ExclusionConstraint` sobre servicio + rango horario), dentro de las fechas de la reserva y del horario de funcionamiento (10:00 a 23:00). Precio habitual de la tinaja: $20.000 a $25.000 cada 30 min (ayuda, no se prellena).
- Bloqueo optimista con campo `version` para evitar que dos usuarios se pisen cambios.
- Adultos y niños dentro de la capacidad del tipo de unidad (ver §4); al menos 1 adulto. Se valida también en la base de datos.
- En reservas directas, el abono se registra con fecha del día en que se crea la reserva.
- En reservas de Airbnb/Booking, los campos sincronizados (fechas, canal, monto) se editan en Beds24, no en la app.
- Cancelaciones de Airbnb/Booking: llegan desde Beds24 en la consulta periódica (cambio de estado de la reserva) y la reserva pasa a cancelada en la app: libera la cabaña y deja de contar en Inicio, por cobrar y el dashboard.
- Cancelar: solo reservas directas confirmadas, con motivo obligatorio. Libera las fechas en Beds24 (con reintentos si falla). Los pagos quedan registrados; las devoluciones se hacen fuera de la app. Una reserva cancelada no cuenta en por cobrar ni en el dashboard.

## 6. Seguridad y privacidad

- Acceso solo por Tailscale. Autenticación de Django + 2FA (TOTP). Un solo rol con todos los permisos de la app para todos los usuarios; cada acción queda registrada con su autor en la auditoría. El superusuario de Django (`is_superuser`, acceso completo a `/admin/`) es solo para la cuenta técnica, no para el uso diario.
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
- Si las tarifas y temporadas se manejan en la app o solo en Beds24.
- Comisiones reales por canal, para calcular el ingreso neto.
- Política de retención de datos de huéspedes.
- Boleta electrónica (SII): si se emite, ¿la app debe registrar el número de boleta de cada pago?
- Qué datos de pago de Airbnb/Booking entrega la API V2 de Beds24 (permiso `bookings-financial`).
- Cancelaciones de Airbnb/Booking con penalidad (el huésped cancela pero igual se recibe una parte): verificar si Beds24 informa ese monto; si lo informa, cuenta como ingreso del mes de la reserva.
