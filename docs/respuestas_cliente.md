# Respuestas del cliente (Maliachi) en #questions — 3 y 4 de noviembre 2026

Fuente: canal #questions de Slack. Resumen para tener claro qué SÍ y qué NO está en el alcance.
Las preguntas del equipo de Hugo vienen numeradas 1–15 en el PDF "Stakeholder Questions" (el cliente responde por número).

## Qué entra (SÍ)

| Tema | Decisión del cliente |
|---|---|
| Acceso | **Cualquier visitante del sitio debe poder donar.** El cliente no entiende "registro con token" → ese flujo no es requisito. |
| Frontend | Se puede usar React u otro framework con Salesforce como backend y portal admin. "Planeen con tiempo si no usan Experience Sites." |
| Campañas | Lista de campañas si hay más de una; si solo hay una, el sitio va directo a ella. Cada campaña: nombre, meta, fecha de inicio y fin (todos obligatorios). Está **activa si la fecha actual cae en su rango**. Cualquier usuario con acceso puede crearlas. |
| Campañas terminadas | Siguen visibles, **en otra vista**. |
| Meta | **No se cambia** una vez activa, haya o no donaciones. |
| Fecha vencida / meta al 100 % | Si pasó la fecha, **no se reciben más donaciones**. Siempre debe haber una campaña abierta. Al llegar al 100 % debe poder decidirse si se aceptan más donaciones. |
| Montos sugeridos | Equipo de Karen propuso $100, $250, $500, $1,000 + monto libre. El cliente agregó **$2k, $5k, $10k, $20k**. |
| Monto mínimo | "Lo más bajo posible; investiguen qué dice la pasarela." (Karen propuso $50; el cliente no lo objetó.) |
| Monto máximo | **Sin máximo**, salvo que la pasarela lo imponga. Si el monto es **≥ $1,000** debe salir un mensaje/diálogo de confirmación destacando el monto. |
| Datos del donante | Solo **nombre y si es empresa**. Para factura, solo lo necesario para emitirla (investigar mínimo legal). |
| Donación anónima | No necesitan saber nada de la persona; solo mandarle el ticket. |
| Comprobante | **El correo de la pasarela basta.** El único correo extra es el de solicitud de factura. |
| Facturas | Aplican a empresas y personas; "cualquiera que pida la factura debe recibirla". Se pide con un **formulario** después de donar y **Salesforce envía la factura** al usuario. |
| Donaciones con factura | Karen's team: deben ser **siempre públicas** (para probar la donación ante el SAT); investigar casos límite. |
| Nombre público | El donante puede escribir lo que quiera (no se puede verificar). Para abusos: lista negra y proceso para que un moderador quite una entrada. |
| Cuántos nombres | "Todos los que haya": lista con autoscroll animado (el equipo puede proponer otra cosa). |
| Registro de donaciones | **Solo se crea el registro después de una transacción exitosa** (14.1). Si la pasarela autorizó, es exitosa. |
| Doble donación accidental | "No creo que sea posible", habría que repetir todo el flujo de pago. |
| Roles internos | Una persona puede **crear donaciones manualmente** (las de fuera); otra puede **cambiar nombre o visibilidad**. Los correos los ven ambos. |

## Qué NO entra (fuera de alcance)

- Reembolsos y pagos por error (pregunta 13): **fuera de alcance**.
- Donaciones que llegan por fuera del sitio (transferencias, proceso manual): se agregan a mano; el sistema no las automatiza.
- Reportes automáticos o personalizados, y límites por campaña: "fuera del alcance" / no respondió.
- Verificación de datos reales del donante: no hay forma, no se pide.

## Sin respuesta clara (preguntar de nuevo o en la llamada)

- Daniel preguntó: ¿existe el flujo de aceptación manual y para qué casos? ¿RF-S3 tiene botón? El cliente respondió "idk what you're asking" (llamada acordada para el 3 nov, 9:30 pm; no hay nota de qué se habló).
- "¿Se personalizan los reportes?": sin respuesta.
- Mínimo exacto de la factura (qué datos): el cliente devolvió la pregunta; hay que investigar.
- Si el monto mínimo de $50 queda confirmado.

## Clase "Discovery Day" (4 nov, noche): lo que se aclaró

Los números entre paréntesis son minutos del video de la clase.

**Las 4 tensiones quedaron resueltas**
1. **Registro antes o después del pago:** mejor después de confirmar (22:38, 38:16). Si se crea antes, copia el estado de Stripe y el sitio no muestra nombre ni monto hasta que se confirme (55:21).
2. **Meta inmutable:** no se programa bloqueo; se resuelve con capacitación y se documenta (1:20).
3. **Recibo:** basta el de Stripe, un solo correo, en español (1:09). Solo la factura la envía Salesforce.
4. **Factura y visibilidad son independientes:** se puede pedir factura y ser anónimo (47:42 a 49:24). Esto reemplaza la propuesta de otro equipo de que factura implique público.

**RF-S3 (confirmar pago a mano):** "fue alucinación, no lo vamos a ni tocar" (46:33). Pedir confirmación por escrito, porque está en la guía.

**Decisiones nuevas**
- Sin token: cualquiera dona (11:26).
- Un sitio con la lista de campañas, más una campaña "Donación general" siempre abierta (58:51). Una campaña cierra solo por fecha; activa = estatus + fechas (1:11).
- Cada campaña lleva nombre, meta, recaudado, descripción, **imagen (must have)** y fecha límite (1:13 a 1:15).
- Datos: anónimo solo correo; público nombre, apellido y correo, o nombre de empresa (30:08). Correo se valida solo por formato; todo se sanea (1:06 a 1:07).
- Factura: mínimo RFC y código postal, lo decidimos nosotros. Consentimiento con un checkbox guardado en Salesforce (52:03). Sin timbrado.
- Montos: $100, $250, $500, $1,000, $2k, $5k, $10k, $20k; mínimo $50; sin máximo salvo Stripe; confirmación desde $1,000 (22:00 a 26:30).
- Tres roles: System Admin, gestor de campañas y moderador. Los tres ven correos; **nadie edita monto ni estatus a mano** (1:03, 1:24).
- Reportes: los estándar. Reembolsos y duplicados: fuera de alcance.

**Cómo trabajar (profesores)**
- Capturar todo como historia de usuario, aunque quede fuera, y documentar por qué (42:24).
- No abrir cajas de Pandora que no cabe en una semana (43:11). Negociar un MVP en los criterios de aceptación (19:42).
- Tablero simple (GitHub Projects o Trello), sin automatizaciones, listo en uno o dos días (6:17, 9:05). Entrega 1: el martes 10 nov.
