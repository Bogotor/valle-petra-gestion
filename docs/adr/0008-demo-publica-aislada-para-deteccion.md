# ADR 0008 — Demo pública aislada para practicar detección

- **Estado:** Aceptada
- **Fecha:** 2026-11-01

## Contexto

Este proyecto también es portafolio de ciberseguridad defensiva (Blue Team / SOC). La app de producción muestra
seguridad desde el diseño, pero no produce evidencia de **detección y respuesta**: para eso hace falta tráfico real
de internet (escaneos, bots, fuerza bruta) y un SIEM que lo analice.

La app de producción **no se expone** (ADR 0002): tiene datos personales de huéspedes y sostiene la operación del
negocio. El SOC con Wazuh es un **proyecto aparte** (homelab), con su propio repositorio.

## Decisión

1. **Producción nunca se expone a internet.** Sigue solo por Tailscale.
2. **Demo pública separada** (fase 5 del SPEC): el mismo código, desplegado en un **VPS distinto**, fuera de la red de
   la casa:
   - base de datos propia con **datos ficticios** que se reinician cada noche;
   - **ningún secreto compartido** con producción; Beds24 siempre en **modo simulado** (sin claves reales);
   - reconstruible desde cero con un script, para poder borrarla y levantarla de nuevo si se compromete.
3. **Endurecimiento base del VPS:** actualizaciones automáticas, SSH solo con llave y sin root, firewall que deja
   pasar solo 80/443, TLS, cabeceras de seguridad y límite de peticiones.
4. **Logs al SOC del homelab:** Nginx, eventos de seguridad de Django (logins fallidos y exitosos, 403, CSRF,
   ráfagas de 404) y el sistema (`auth.log`) llegan a Wazuh mediante su agente, **por Tailscale**. Una ACL de
   Tailscale deja que el VPS alcance **solo** los puertos del manager de Wazuh (1514 y 1515), nada más del homelab.
   El manager no se expone a internet.
5. **Solo defensa.** Bloquear y alertar (incluida la *active response* de Wazuh), nunca contraatacar: el
   "hack back" es delito según la Ley 21.459.
6. **Lo que se publica** (casos de estudio, métricas): sin IPs, nombres de equipos ni configuración del homelab. Las
   IPs de origen de los ataques se agregan (por país o red) o se anonimizan.

## Alternativas consideradas

| Opción | A favor | Por qué no |
|---|---|---|
| Exponer la app de producción | Tráfico real sin costo extra | Pone en riesgo datos de huéspedes y la operación familiar; contradice ADR 0002 |
| Solo laboratorios simulados en el homelab | Gratis y controlado | Útil para aprender, pero los ataques los genera uno mismo: no muestra triaje de tráfico real |
| Honeypot genérico (por ejemplo, T-Pot) | Mucho tráfico, poco esfuerzo | No muestra la defensa de una aplicación propia; puede sumarse después como complemento |

## Consecuencias

- ✅ Dos proyectos de portafolio que se entienden por separado y se conectan en un punto: el homelab SOC vigila un
  servicio real expuesto (la demo).
- ✅ Material para casos de estudio: reglas y decoders propios, mapeo a MITRE ATT&CK, falsos positivos ajustados.
- ⚠️ Costo y mantenimiento del VPS (del orden de US$5 al mes).
- ⚠️ Si el VPS se compromete, el daño queda acotado: datos ficticios, sin secretos de producción y acceso al homelab
  limitado por la ACL a los puertos de Wazuh.
- 🔁 Depende de dos cosas previas: Wazuh funcionando en el homelab y la app con buen logging (fases 1 a 3).
