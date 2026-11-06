# Backlog — Portal de donaciones Mexico Dreamin

Borrador generado a partir de los Lineamientos (secciones 4 y 8), actualizado el 5 nov con las respuestas del cliente
en #questions (resumen en `docs/respuestas_cliente.md`). Lo que viene del cliente se marca **[cliente]**; las tensiones
con la guía se marcan ⚠️ y se confirman con el profesor. Los criterios `CA-mín` son los mínimos
de la guía y no se pueden debilitar; los `CA-prop` son propios del equipo (incluyen casos negativos).

**Estado:** una historia solo es *cumplida* cuando TODOS sus criterios están demostrados con evidencia
(demo, video o captura) en la org de prueba. Hasta entonces: `No cumplida`.
**Clase "Discovery Day" (4 nov, noche):** el cliente y los profesores aclararon las tensiones de abajo; se marcan **[clase]**
con el minuto del video. Decisiones clave: registro tras confirmar el pago, RF-S3 descartado, sin bloqueos de meta
(se resuelve con capacitación), recibo solo de Stripe, factura y visibilidad son decisiones independientes, 3 roles, imagen
en cada campaña, "Donación general" siempre abierta. Detalle en `docs/respuestas_cliente.md`.

La columna **Código** dice qué hay en el repo hoy (sin verificar contra una org).

| ID | RF | Hito | Historia | Estado | Código (sin verificar) |
|---|---|---|---|---|---|
| HU-01 | RF-D1a | E1 | Elegir campaña y ver su avance | No cumplida | Parcial: `obtenerAvance`, `mesaMuro`; falta lista de campañas y montos sugeridos |
| HU-02 | RF-D1b | E2 | Donar cualquier monto | No cumplida | Parcial: monto abierto 100–1,000,000 y paquetes; sin pasarela |
| HU-03 | RF-D2 | E1 | Donar de forma anónima | No cumplida | Parcial: solo "Privada"; staff no la ve como anónima; sin comprobante |
| HU-04 | RF-D3 | E1 | Registro antes de pagar | No cumplida | Parcial: Opportunity `Prospecting`; sin referencia hacia la pasarela |
| HU-05 | RF-D4 | E1 | Página de agradecimiento | No cumplida | No existe |
| HU-06 | RF-D5 | E2 | Confirmación y comprobante automáticos | No cumplida | Parcial: `confirmarPago` idempotente; sin webhook ni correo |
| HU-07 | RF-C1 | E1 | Aparecer como donante con crédito | No cumplida | Parcial: `Reconocimiento_Publico__c` + muro |
| HU-08 | RF-F1 | E1 | Capturar nombre y RFC para factura | No cumplida | No existe |
| HU-09 | RF-F2 | E1 | Staff marca/filtra solicitudes de factura | No cumplida | No existe |
| HU-10 | RF-S1 | E1 | Administrar campañas y meta | No cumplida | Parcial: script `crear-campana.apex`; meta en `ExpectedRevenue` |
| HU-11 | RF-S2 | E1 | Ver todas las donaciones | No cumplida | Sin vista; solo permission set |
| HU-12 | RF-S3 | E1 | Confirmar un pago a mano | Descartada [clase] ⚠️ | Parcial: `@InvocableMethod`; sin Flow/botón ni correo |
| HU-13 | RF-S4 | E2 | Ver confirmaciones automáticas y avisos rechazados | No cumplida | No existe |
| HU-14 | RF-V1 | E1 | Sitio público acotado, sin login | No cumplida | Parcial: LWC + clase guest; sitio sin verificar |
| HU-15 | RF-V2 | E1 | Prueba de seguridad del visitante | No cumplida | Diseño por token; sin prueba documentada |
| HU-16 | RF-V3a | E2 | Recibir el aviso de pago | No cumplida | No existe |
| HU-17 | RF-V3b | E2 | Solo avisos auténticos y sin duplicados | No cumplida | Idempotencia en `confirmarPago`; sin firma ni log de rechazos |
| HU-18 | (propia) | E1 | Donación abandonada/pendiente | No cumplida | No existe (pregunta abierta Q2) |
| HU-19 | (propia) | E1 | Error entendible sin dejar datos a medias | No cumplida | Parcial: `AuraHandledException` con mensajes |
| HU-20 | (propia) | E2 | Clasificación de datos personales | No cumplida | Sin documento; ver `System.debug` con datos del donante |
| HU-21 | (propia) [cliente] | E1 | Moderar nombres del reconocimiento público | No cumplida | No existe |
| HU-22 | (propia) [cliente] | E2 | Registrar a mano una donación recibida fuera del sitio | No cumplida | No existe |
| HU-23 | (propia) [cliente][clase] | E1 | Tres roles internos: admin, campañas, moderador | No cumplida | Solo `Mesa_Regalos_Admin` |
| HU-24 | (propia) [clase] | E1 | Campaña "Donación general" siempre abierta | No cumplida | No existe |
| HU-25 | (propia) [clase] | E1 | Imagen y descripción en cada campaña | No cumplida | No existe |
| HU-26 | (propia) [clase] | E1 | Consentimiento para usar datos de factura | No cumplida | No existe |
| HU-27 | (propia) [clase] | E2 | Historial de campañas terminadas (nice-to-have) | No cumplida | No existe |

---

## Donante (visitante sin login)

### HU-01 · RF-D1a · E1 · Elegir campaña y ver su avance
Como donante, quiero elegir una campaña y ver cuánto lleva de su meta, para decidir a cuál donar.
- **CA-mín 1.** Dado que hay ≥2 campañas activas con meta, cuando entro sin login, entonces veo cada una con su meta y su avance.
- **CA-mín 2.** Dado que una donación pasa a pagada, cuando recargo, entonces el avance incluye ese monto; las pendientes no cuentan.
- **CA-mín 3.** Dado que elegí campaña, cuando continúo, entonces puedo indicar monto sugerido o capturado en la pasarela.
- **CA-prop 4.** Dada una campaña con meta 0 o vacía, cuando se muestra, entonces no hay división por cero ni porcentaje inválido.
- **CA-prop 5.** Dada una campaña inactiva, cuando entro, entonces no aparece como opción de donación.
- **CA-prop 6.** Dado que el avance supera la meta, cuando se muestra, entonces el porcentaje se topa en 100 %.
- **CA-prop 7 [cliente].** Dado que hay una sola campaña activa, cuando entro al sitio, entonces voy directo a ella (sin lista).
- **CA-prop 8 [cliente].** Dada una campaña cuya fecha de fin ya pasó, cuando entro, entonces no puedo donarle y aparece en la vista de campañas terminadas, que sigue visible.
- **CA-prop 9 [cliente].** Dada una campaña que llegó al 100 %, cuando se evalúa, entonces existe una forma de decidir si sigue recibiendo donaciones (ver pregunta Q6).

### HU-02 · RF-D1b · E2 · Donar cualquier monto
Como donante, quiero donar el monto que yo decida, para aportar lo que puedo y no solo los sugeridos.
- **CA-mín 1.** Dada una campaña elegida, cuando capturo un monto > 0, entonces la pasarela de prueba cobra exactamente ese monto y Salesforce registra el mismo monto.
- **CA-prop 2.** Dado un monto 0, negativo, vacío o no numérico, cuando envío, entonces se rechaza con mensaje claro y no se crea donación.
- **CA-prop 3.** Dado un monto con coma o decimales (`1,500`, `99.999`), cuando envío, entonces se interpreta o rechaza de forma consistente entre navegador y servidor.
- **CA-prop 4.** Dado que cambio el monto en el navegador después de validar, cuando el servidor registra, entonces prevalece el monto validado por el servidor.
- **CA-prop 5 [cliente].** Dado que entro a donar, cuando veo los montos sugeridos, entonces aparecen $100, $250, $500, $1,000, $2,000, $5,000, $10,000 y $20,000, además de monto libre.
- **CA-prop 6 [cliente].** Dado un monto por debajo del mínimo (el más bajo que permita la pasarela; propuesta: $50), cuando envío, entonces se rechaza y veo el rango permitido antes de donar.
- **CA-prop 7 [cliente].** Dado que no hay tope máximo (salvo el de la pasarela), cuando capturo un monto de $1,000 o más, entonces aparece un diálogo de confirmación que destaca el monto antes de continuar.

### HU-03 · RF-D2 · E1 · Donar de forma anónima
Como donante anónimo, quiero que mi nombre no aparezca en ningún lugar público, para apoyar sin exponerme.
- **CA-mín 1.** Dado que marqué anónima, cuando reviso el sitio público (campañas, reconocimiento, agradecimiento), entonces mi nombre no aparece.
- **CA-mín 2.** Dado que mi donación anónima está pagada, cuando recibo el comprobante, entonces no muestra mi nombre.
- **CA-mín 3.** Dado que marqué anónima, cuando intento solicitar factura, entonces no puedo.
- **CA-mín 4.** Dado que existe una donación anónima, cuando staff la consulta, entonces la ve identificada como anónima.
- **CA-prop 5.** Dada una donación anónima, cuando se consulta la API/Apex pública por cualquier método, entonces no devuelve nombre, correo ni LinkedIn.
- **CA-prop 6.** Dada una donación anónima pagada con monto alto, cuando se muestra el avance, entonces el monto suma al total pero no se asocia a persona alguna.
- **CA-prop 7 [cliente].** Dado un donante anónimo, cuando dona, entonces no se le pide ningún dato personal más allá de lo que la pasarela requiere para enviarle su ticket.

### HU-04 · RF-D3 · E1 · Registro antes de pagar
> **[clase, 22:38 y 38:16]** Lo ideal es crear el registro *después* de que Stripe confirma. Si se crea antes (RF-D3), debe copiar el estado de Stripe y **no mostrarse en el sitio (nombre ni monto) hasta estar confirmado**. Nuestra decisión: crear la donación *Pendiente* con referencia antes de abrir Stripe (cumple los CA-mín), invisible para el público, y pasarla a *Pagada* solo con el aviso firmado de Stripe.

Como donante, quiero que mi donación quede registrada antes de pagar, para que el programa reconozca mi pago cuando llegue.
- **CA-mín 1.** Dado que llené datos y elegí campaña, cuando envío, entonces existe en Salesforce una donación pendiente con campaña, modalidad y monto (si ya se conoce) antes de abrir la pasarela.
- **CA-mín 2.** Dado que envié el formulario, cuando se abre la pasarela de prueba, entonces el pago lleva una referencia que permite a staff encontrar la donación.
- **CA-prop 3.** Dado que el formulario es inválido (sin campaña, sin aviso aceptado), cuando envío, entonces no se crea la donación pendiente.
- **CA-prop 4.** Dado que doy doble clic en "Donar", cuando se procesa, entonces se crea una sola donación pendiente.

### HU-05 · RF-D4 · E1 · Página de agradecimiento
Como donante, quiero ver una página de agradecimiento al terminar de pagar, para saber que mi donación se recibió.
- **CA-mín 1.** Dado que completé el pago de prueba, cuando regreso al sitio, entonces veo la página de agradecimiento.
- **CA-mín 2.** Dado que el pago aún no está confirmado en Salesforce, cuando veo la página, entonces no dice que está confirmado.
- **CA-mín 3.** Dado que estoy en la página, cuando la reviso, entonces no hay datos de otros donantes.
- **CA-prop 4.** Dado que abro la URL de agradecimiento sin haber pagado, cuando carga, entonces no afirma ningún pago.
- **CA-prop 5.** Dado que cancelo en la pasarela, cuando regreso, entonces veo mensaje de pago no completado y la donación sigue pendiente.

### HU-06 · RF-D5 · E2 · Confirmación y comprobante automáticos
Como donante, quiero que mi donación se confirme y recibir mi comprobante sin que nadie lo revise a mano, para tener la constancia en cuanto pago.
- **CA-mín 1.** Dado que pagué con tarjeta de prueba, cuando la pasarela confirma, entonces la donación pasa a pagada sin intervención de staff.
- **CA-mín 2.** Dado que pasó a pagada, cuando reviso mi correo, entonces recibo un comprobante que me identifica la donación.
- **CA-prop 3.** Dado que mi tarjeta de prueba es rechazada, cuando la pasarela responde, entonces la donación no pasa a pagada y no recibo comprobante.
- **CA-prop 4.** Dado que el correo no se puede enviar, cuando falla, entonces la donación queda pagada y el fallo queda visible para staff.
- **CA-prop 5 [cliente].** El comprobante es el recibo de la pasarela; el único correo adicional es el de solicitud de factura (ver HU-08). ⚠️ Confirmar que esto cumple CA-mín 2.

## Donante con crédito

### HU-07 · RF-C1 · E1 · Aparecer como donante
- **CA-mín 1.** Dado que pedí crédito y mi donación está pagada, cuando un visitante abre la campaña, entonces ve mi nombre y monto en el reconocimiento de esa campaña (lista solo de pagadas).
- **CA-mín 2.** Dado que pedí crédito, cuando se revisa el reconocimiento, entonces solo aparecen nombre y monto (ni correo ni otro dato).
- **CA-prop 3.** Dada una donación con crédito aún pendiente, cuando se abre el reconocimiento, entonces no aparece.
- **CA-prop 4.** Dado un nombre muy largo o con HTML/script, cuando se muestra, entonces se trunca o escapa sin romper la página.
- **CA-prop 5 [cliente].** Dado que el donante escribe el nombre público que quiera (no se verifica), cuando se paga, entonces aparece tal cual, sujeto a moderación (HU-21).
- **CA-prop 6 [cliente].** Dados varios donantes con crédito, cuando se muestra el reconocimiento, entonces aparecen todos, en una lista con autoscroll animado.

## Donante con factura

### HU-08 · RF-F1 · E1 · Capturar datos para factura
- **CA-mín 1.** Dado que elegí factura, cuando envío sin nombre o sin RFC, entonces no puedo enviar y veo qué falta.
- **CA-mín 2.** Dado que capturé un RFC, cuando lo envío, entonces se valida contra el patrón SAT (12 caracteres persona moral / 13 persona física; letras, fecha, homoclave) y se rechaza si no cumple. No se consulta al SAT.
- **CA-mín 3.** Dado que completé la donación con factura, cuando se revisa el sistema, entonces no se emite ningún CFDI.
- **CA-prop 4.** Dado un RFC en minúsculas o con espacios, cuando lo envío, entonces se normaliza o rechaza de forma consistente.
- **CA-prop 5.** Dado el RFC capturado, cuando un visitante consulta cualquier página pública, entonces no aparece.
- **CA-prop 6 [cliente].** La factura aplica a personas y a empresas; solo se piden los datos necesarios para emitirla (el mínimo lo investiga el equipo).
- **CA-prop 7 [cliente].** Dado que el donante pide factura, cuando envía un formulario posterior a la donación, entonces Salesforce le envía la factura por correo (único correo extra además del recibo de la pasarela).
- **CA-prop 8 ⚠️.** El equipo de Karen propuso que las donaciones con factura sean siempre públicas (para probar el donativo ante el SAT); el cliente lo encontró razonable. Falta confirmar si el nombre público lo elige el donante (ver Q1).

### HU-09 · RF-F2 · E1 · Marcar la solicitud de factura
- **CA-mín 1.** Dado que hay una donación con factura, cuando staff la consulta, entonces la ve marcada "solicita factura" con nombre y RFC.
- **CA-mín 2.** Dado que hay donaciones de varias modalidades, cuando staff busca las que solicitan factura, entonces ve solo esas.

## Staff

### HU-10 · RF-S1 · E1 · Administrar campañas
- **CA-mín 1.** Dado que soy staff, cuando creo una campaña con nombre y meta, entonces aparece en el sitio público.
- **CA-mín 2.** Dada una campaña, cuando staff cambia su meta, entonces el sitio muestra la meta nueva.
- **CA-prop 3.** Dado un usuario sin rol de staff, cuando intenta crear o editar una campaña, entonces no puede.
- **CA-prop 4 [cliente].** Dada una campaña nueva, cuando se crea, entonces exige nombre, meta, fecha de inicio y fecha de fin; está activa solo si la fecha actual cae en ese rango.
- **CA-prop 5 [cliente][clase].** Dada una campaña ya activa, la meta no debe cambiarse; **no se construye un bloqueo** (el profesor pidió resolverlo con capacitación y documentarlo, 1:20). Cumple CA-mín 2 porque el sitio siempre muestra la meta vigente.
- **CA-prop 6 [clase].** Dada una campaña, cuando staff cambia su estatus (p. ej. pausar), entonces deja de aceptar donaciones sin tocar sus fechas. Activa = estatus activo **y** fecha dentro del rango.

### HU-11 · RF-S2 · E1 · Ver todas las donaciones
- **CA-mín 1.** Dadas donaciones de las tres modalidades y en distintos estados, cuando staff consulta, entonces ve estado, monto, modalidad y campaña de cada una.
- **CA-prop 2.** Dada una donación anónima, cuando staff consulta, entonces aparece como anónima, no con nombre.

### HU-12 · RF-S3 · E1 · Confirmar un pago a mano
> ⚠️ **[clase, 46:33]** El profesor dijo que RF-S3 fue "alucinación" y "no lo vamos a ni tocar"; el estado del pago lo decide Stripe. Está en la guía como mínimo: **pedirle por escrito que confirme que no se evalúa** antes de quitarlo (Q7). Hasta entonces los criterios se conservan, sin implementar.
- **CA-mín 1.** Dado que una donación está pendiente, cuando staff confirma el pago, entonces pasa a pagada.
- **CA-mín 2.** Dado que staff confirmó, cuando el donante revisa su correo, entonces recibe comprobante.
- **CA-prop 3.** Dada una donación ya pagada, cuando staff vuelve a confirmar, entonces no se duplica ni el estado ni el comprobante.

### HU-13 · RF-S4 · E2 · Ver confirmaciones automáticas y avisos rechazados
- **CA-mín 1.** Dada una donación confirmada por aviso de pasarela, cuando staff la consulta, entonces distingue que fue automática y no manual.
- **CA-mín 2.** Dado un aviso rechazado, cuando staff revisa los rechazados, entonces ve cuándo llegó y por qué se rechazó.

## Visitante y seguridad

### HU-14 · RF-V1 · E1 · Sitio público acotado
- **CA-mín 1.** Sin sesión, al abrir la dirección veo campañas sin que se pida login.
- **CA-mín 2.** Al recorrer todas las páginas solo encuentro campañas, avances, reconocimiento (nombre y monto de quien pidió crédito) y formulario.

### HU-15 · RF-V2 · E1 · Prueba de seguridad del visitante
- **CA-mín 1.** Con donaciones de otras personas existentes, un visitante sin login que intenta leerlas (por el sitio y por cualquier vía identificada) no obtiene ningún dato.
- **CA-mín 2.** El mismo visitante que intenta modificarlas falla.
- **CA-mín 3.** Otra persona, siguiendo los pasos del documento de seguridad, repite la prueba y obtiene el mismo resultado.

### HU-16 · RF-V3a · E2 · Recibir el aviso de pago
- **CA-mín 1.** Dado que un donante pagó con tarjeta de prueba, cuando la pasarela envía su aviso, entonces llega a Salesforce y la donación pasa a pagada.

### HU-17 · RF-V3b · E2 · Avisos auténticos, sin duplicados
- **CA-mín 1.** Dado un aviso falso (no de la pasarela), cuando llega, entonces se rechaza, la donación no cambia y el rechazo queda visible para staff (HU-13).
- **CA-mín 2.** Dado el mismo aviso dos veces, cuando llegan ambos, entonces hay una sola donación pagada y un solo comprobante.

## Historias propias

### HU-18 · Donación abandonada o nunca pagada · E1
Como tesorería, quiero saber qué pasa con una donación pendiente que nunca se paga, para no inflar el avance ni dejar basura.
> [cliente][clase] "Pendiente" solo existe hasta que la pasarela autoriza y no se muestra en el sitio mientras tanto (55:21). Que Stripe decida el estado; nosotros solo copiamos.
- **CA 1.** Dada una donación pendiente que la pasarela no autorizó (cancelada o abandonada), cuando se revisa, entonces queda marcada como cancelada/vencida; el plazo exacto se define con el profesor.
- **CA 2.** Dada una donación vencida, cuando se consulta el avance, entonces no suma.

### HU-19 · Errores entendibles, datos coherentes · E1
- **CA 1.** Dado que falla un dato requerido, la pasarela o la automatización, cuando ocurre, entonces la persona ve un mensaje entendible y la donación no queda en un estado incoherente (demostrar al menos un caso).

### HU-20 · Clasificación de datos personales (OA6) · E2
- **CA 1.** Existe una tabla con cada dato del donante (incluidos los de pago): si es personal, si se guarda, por qué y quién lo ve.
- **CA 2.** Ningún dato personal se escribe en logs de depuración ni en capturas del repo.

### HU-21 · Moderar nombres del reconocimiento público [cliente][clase] · E1
Como moderador, quiero revisar y editar el nombre que se muestra, para que nadie use el muro para ofender.
- **CA 1.** Dado un nombre inapropiado ya publicado, cuando el moderador lo oculta o lo edita, entonces el sitio público muestra el cambio y el registro de la donación se conserva.
- **CA 2.** Dada una lista de palabras bloqueadas, cuando un donante escribe un nombre que la contiene, entonces no se publica sin revisión.
- **CA 3.** Dado un visitante sin login, cuando intenta quitar o editar una entrada, entonces falla.
- **CA 4 [clase, 1:06 a 1:07].** Dado cualquier texto del formulario (nombre, apellido), cuando se guarda, entonces se sanea (HTML, inyección SOQL); el correo solo se valida por formato.
- Nota: el donante puede escribir el nombre que quiera, no hay forma de verificarlo.

### HU-22 · Registrar a mano una donación recibida fuera del sitio [cliente] · E2 (puntos extra)
Como staff, quiero registrar manualmente una donación que llegó por fuera (transferencia, depósito, OXXO), para que la meta refleje todo lo recibido.
- **CA 1.** Dado que recibí una donación fuera del sitio, cuando la registro con campaña, monto y modalidad, entonces queda como pagada y suma al avance.
- **CA 2.** Dada una donación manual, cuando se consulta, entonces se distingue de las pagadas por la pasarela (RF-S4).
- Nota [clase, 41:30 y 42:12]: **fuera del alcance** ("brownie points"); se captura como historia y se documenta por qué quedó fuera.

### HU-23 · Tres roles internos [cliente][clase] · E1
Como coordinación, quiero separar permisos por rol, para limitar el riesgo de errores. Roles propuestos en clase (1:03 a 1:04): **System Admin**, **gestor de campañas** y **moderador**.
- **CA 1.** Dado el gestor de campañas, cuando crea o edita una campaña, entonces puede; no puede editar nombres públicos.
- **CA 2.** Dado el moderador, cuando cambia el nombre público, entonces puede; no puede crear campañas.
- **CA 3.** Dados los tres roles, cuando consultan donaciones, entonces ven el correo del donante.
- **CA 4 [clase, 1:24].** Dado cualquier usuario, cuando intenta cambiar monto o estatus de pago a mano, entonces no puede: solo el sistema (Stripe) los modifica.

### HU-24 · Campaña "Donación general" siempre abierta [clase, 58:51] · E1
- **CA 1.** Dado que no hay campañas activas, cuando un visitante entra, entonces puede donar a la campaña general.
- **CA 2.** Dada la campaña general, cuando se muestra, entonces no tiene meta ni fecha de cierre.

### HU-25 · Imagen y descripción en cada campaña [clase, 1:13 a 1:15] · E1
El profesor la marcó **must have**: ver la causa aumenta la donación.
- **CA 1.** Dada una campaña, cuando el visitante la ve, entonces muestra nombre, imagen, descripción, meta, recaudado y fecha límite.
- **CA 2.** Dada una campaña sin imagen, cuando se crea, entonces se avisa a staff (no se publica vacía).

### HU-26 · Consentimiento para usar datos de factura [clase, 52:03] · E1
- **CA 1.** Dado un donante que pide factura, cuando envía, entonces debe marcar "Acepto que mis datos se usen para emitir mi factura".
- **CA 2.** Dado que marcó el consentimiento, cuando se guarda la donación, entonces el consentimiento queda **guardado en Salesforce** (no solo en el navegador).
- **CA 3.** Los datos fiscales (RFC y código postal como mínimo, decisión nuestra) se usan solo para emitir la factura. Sin timbrado ni firma electrónica.

### HU-27 · Historial de campañas terminadas · E2 (nice-to-have) [clase, 1:16]
- **CA 1.** Dada una campaña terminada, cuando el visitante abre la pestaña de terminadas, entonces ve cómo quedó y quiénes donaron (sin anónimos).

### Fuera de alcance (capturado a propósito, [clase, 42:24])
Reembolsos y cancelaciones (1:05:39), donaciones duplicadas por accidente, reportes personalizados (se usan los estándar), montos sugeridos editables por el cliente (se dejan fijos en código), máximo de donación (solo el límite de Stripe, por revisar), internacionalización (todo en español; opcional dejar Custom Labels listos), emisión de factura con timbrado.

---

## Preguntas abiertas para el cliente (registrar la respuesta aquí)

| # | Pregunta | Respuesta | Fecha |
|---|---|---|---|
| Q1 | ¿Un donante con factura puede pedir no aparecer en el reconocimiento público? | **[clase, 47:42 a 49:24]** Son decisiones independientes: se puede pedir factura y ser anónimo. Se pregunta por separado. | 5 nov |
| Q2 | ¿Qué pasa con una donación pendiente que nunca se paga y en cuánto tiempo caduca? | "Pendiente" solo es hasta que la pasarela autoriza; no hay pendiente indefinido. Revisar la documentación de Stripe. | 4 nov |
| Q3 | ¿El comprobante qué debe contener (monto, campaña, referencia, fecha)? | Basta el recibo de la pasarela. El único correo extra es el de la factura. | 4 nov |
| Q4 | ¿Los montos sugeridos y el mínimo/máximo de donación los define el cliente? | Sugeridos $100–$20k (8 opciones) + monto libre; mínimo el más bajo que permita la pasarela; sin máximo; confirmación desde $1,000. | 4 nov |
| Q5 | ¿El donante anónimo recibe comprobante por correo? ¿Con qué datos? | No se pide nada de la persona; solo se le envía el ticket (recibo de la pasarela). | 4 nov |
| Q6 | Si una campaña llega al 100 %, ¿cómo se decide si sigue recibiendo donaciones? | **[clase, 24:03 y 58:08]** Sigue recibiendo; solo se cierra por fecha o estatus. | 5 nov |
| Q7 | RF-S3: ¿existe el botón de confirmación manual y en qué casos? | **[clase, 46:33]** "Fue alucinación, no lo vamos a ni tocar." Falta confirmar por escrito que no cuenta como mínimo. | 5 nov |
| Q8 | ⚠️ ¿Cómo cumplir RF-D3 (pendiente antes de pagar) si el cliente quiere el registro solo tras el pago exitoso? | **[clase, 22:38 y 38:16]** Mejor crear después del pago; si se crea antes, copia el estado de Stripe y no se muestra. Ver HU-04. | 5 nov |
| Q9 | ⚠️ ¿La meta puede editarse antes de activarse (RF-S1 CA2) aunque no después? | **[clase, 1:20]** No se construye bloqueo; capacitación y documentarlo. | 5 nov |

## Cobertura por modalidad (la guía exige demostrar las tres)

| Paso | Anónimo | Con crédito | Con factura |
|---|---|---|---|
| Elegir campaña y monto | HU-01, 02 | HU-01, 02 | HU-01, 02 |
| Registro antes de pagar | HU-04 | HU-04 | HU-04 + HU-08 |
| Agradecimiento | HU-05 | HU-05 | HU-05 |
| Confirmación y comprobante | HU-06 | HU-06 | HU-06 |
| Lo que ve el público | HU-03 | HU-07 | HU-03/07 según Q1 |
| Lo que ve staff | HU-03, 11 | HU-11 | HU-09, 11 |
