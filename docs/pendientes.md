# Pendientes vs. rúbrica

Fechas: **Entrega 1 mar 10 nov** · **Entrega 2 mar 17 nov** · **Defensa mié 18 nov** · Evento vie 20 nov. Hora límite 23:59 (Mérida).
Estado según el repo, las respuestas del cliente en #questions (3 y 4 nov) y la clase Discovery Day del 4 nov (sin verificar contra una org).

## 0. Mínimos (pasa / no pasa, −10 pts de equipo cada uno en la Entrega 2)

- [ ] **M1** Verificar que todo corre en modo de prueba (Stripe test, tarjetas de prueba, datos ficticios).
- [ ] **M2** Prueba de seguridad del visitante, repetible por el profesor (ver §5).
- [ ] **M3** Revisar el **historial de commits** completo buscando claves (`git log -p | grep -iE "sk_|pk_|whsec_|secret"`). El repo es público.

## 1. Antes de la Entrega 1 (10 nov)

**Sitio y flujo de donación**
- [ ] Donación **sin token**: un formulario único (cliente y clase confirmaron que cualquiera dona).
- [ ] Tablero de historias (GitHub Projects, simple) listo antes del viernes; documentar qué queda dentro y fuera del alcance.
- [ ] Lista de campañas con **imagen**, descripción, meta, avance y fecha; con una sola campaña, ir directo a ella; campaña "Donación general" siempre abierta.
- [ ] Campañas con nombre, meta, inicio y fin obligatorios; activa = estatus activo y fecha dentro del rango (poder pausar sin tocar fechas).
- [ ] Montos sugeridos $100, $250, $500, $1,000, $2k, $5k, $10k y $20k, más monto libre; mínimo según la pasarela; sin máximo; confirmación desde $1,000.
- [ ] Modalidad anónima o con crédito, más un interruptor independiente "quiero factura" (hoy solo existe Pública/Privada). Anónimo pide solo correo.
- [ ] Factura: nombre, RFC y código postal con validación del patrón SAT (RF-F1), checkbox de consentimiento guardado en Salesforce y marca "solicita factura" visible para staff (RF-F2).
- [ ] Donación anónima: staff la ve como anónima; no se puede pedir factura (RF-D2).
- [ ] Registro de la donación con una **referencia** que viaje a la pasarela (RF-D3). Ver la tensión en §6.
- [ ] Página de agradecimiento que no afirma un pago no confirmado (RF-D4).
- [ ] Vista de staff: administrar campañas y listar donaciones (RF-S1, S2). RF-S3 queda en espera de confirmación (§7).
- [ ] Tres roles: admin, gestor de campañas y moderador; nadie edita montos ni estatus de pago a mano.
- [ ] Moderación de nombres públicos (lista negra y editar u ocultar) y lista con autoscroll.
- [ ] Sanear todo texto del formulario y validar el correo por formato.
- [ ] Cuenta de Stripe en modo prueba, sin activarla.

**Documentación**
- [ ] Crear `docs/` con el backlog enlazado desde el README, incluido el % de avance y cómo se calculó.
- [ ] `docs/seguridad.md`: prueba del visitante (pasos, esperado, obtenido, evidencia), permisos del guest y su porqué, dónde están las claves, clasificación de datos personales, riesgos conocidos (incl. spam).
- [ ] `docs/bitacora_ia.md`: fecha, autor, herramienta, historia, prompt, qué verificaron, qué corrigieron, aceleración estimada. **Mínimo 3 entradas con error de la IA**, de los tres integrantes.
- [ ] `docs/contribucion.md` (una tabla por hito, con evidencia).
- [ ] `docs/preguntas_y_reportes.md` (≥4 entradas por persona, en ≥2 semanas distintas, con qué pasó después).
- [ ] Etiqueta `entrega-1` y aviso en el canal de entregas con el enlace al repo.

**Limpieza técnica**
- [ ] Quitar los `System.debug('DATOS RECIBIDOS…')` de `MesaRegalosController` (datos personales en logs).
- [ ] Quitar el Id de Opportunity hard-coded del comentario de `simulador-donativo.apex`.
- [ ] Mover montos, paquetes y límites a un solo lugar (hoy están duplicados en Apex y en `mesaDonacion`).
- [ ] Ejecutar Code Analyzer y atender o justificar cada hallazgo.
- [ ] Respaldos semanales de metadata (`sf project retrieve`) subidos al repo; al menos un integrante entra a la org cada semana.

## 2. Antes de la Entrega 2 (17 nov)

- [ ] Webhook de Stripe → Salesforce que deja la donación pagada sin intervención (RF-D5, V3a).
- [ ] Comprobante: decidido, basta el recibo de Stripe (un solo correo, en español); el único extra es el de la factura.
- [ ] Revisar en la documentación de Stripe el monto máximo por pago y el mínimo para México.
- [ ] Rechazar avisos falsos (firma) y deduplicar avisos repetidos (RF-V3b, segundo nivel).
- [ ] Registro de avisos rechazados y distinción automática/manual para staff (RF-S4).
- [ ] Manejo de errores demostrado en al menos un caso (pago cancelado, aviso fallido, correo no sale).
- [ ] Bulkificación: sin consultas ni escrituras en ciclos (hoy `confirmarPago` consulta `CampaignMemberStatus` por miembro).
- [ ] Actualizar `docs/seguridad.md` para el aviso de la pasarela (secretos del webhook, permisos del usuario de integración).
- [ ] Etiqueta `entrega-2` y bitácoras actualizadas con entradas de los tres.

## 3. Defensa oral (18 nov), individual

- [ ] Explicar tu parte sin leer y mostrarla funcionando.
- [ ] Justificar una decisión de diseño y la alternativa descartada.
- [ ] Contar un caso en que corregiste a la IA.
- [ ] Todo código generado por IA debe poder explicarse.

## 4. Equipo: contribución (pesa en C9 y en la defensa)

- [ ] El historial muestra un solo autor. Los otros integrantes necesitan commits propios ligados a historias, o contribución documentada en `docs/contribucion.md`.

## 5. Prueba de seguridad del visitante (M2 / RF-V2)

- [ ] Con donaciones de otras personas en la org, intentar **leer** como guest (UI, llamadas Apex directas, REST, SOQL vía `/services/data`) y **modificar**.
- [ ] Guardar pasos, resultado esperado/obtenido y evidencia, para que otra persona los repita.
- [ ] Documentar que `MesaRegalosController` es `without sharing` y por qué; revisar que `obtenerMuro` y `obtenerAvance` no devuelvan nada de donantes anónimos.

## 6. Tensiones con la guía (resueltas en la clase del 4 nov)

1. **Registro antes vs. después del pago:** crear después de confirmar; si se crea antes (RF-D3), copiar el estado de Stripe y no mostrarlo. Plan: *Pendiente* invisible con referencia, que pasa a *Pagada* solo por el aviso firmado.
2. **Meta inmutable vs. RF-S1 CA2:** sin bloqueo en el sistema; capacitación y documentarlo.
3. **Comprobante:** el recibo de Stripe basta.
4. **Factura vs. visibilidad:** decisiones independientes.

## 7. Preguntas que siguen abiertas

1. **RF-S3:** la clase dijo que no se toca. Está en la guía como mínimo: pedir por escrito que no se evalúa.
2. ¿Cuál es el monto máximo y mínimo real de Stripe? (Lo debemos investigar nosotros.)
3. Para el webhook de Stripe en un Developer Edition: ¿se acepta un endpoint de Apex REST con acceso de guest? ¿Y guardar el secreto de firma en Custom Metadata sin acceso para el guest?
4. ¿Cómo debemos registrar en la bitácora de IA el código que se generó antes de la guía?
5. ¿Plazo para cancelar una donación pendiente que nunca se paga? (Stripe decide el estado; falta definir cuándo la damos por vencida.)

Las respuestas del cliente y de la clase están resumidas en `docs/respuestas_cliente.md`.
