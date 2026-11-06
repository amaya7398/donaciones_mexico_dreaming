# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es

"Mesa de regalos · México Dreaming": proyecto SFDX (API 62.0, un solo paquete `force-app`) para que ex-alumnos actualicen sus datos y registren un donativo desde un sitio público de Experience Cloud. El README (en español) es la fuente de verdad del flujo y del despliegue. El sitio ya está desplegado y funciona en una org de desarrollo (lo mostró el equipo), pero no hay pruebas ni evidencia documentadas; tratar el código como no verificado.

## Comandos

Org de trabajo de ejemplo: alias `mesa-dev` (Developer Edition).

```bash
sf org login web --alias mesa-dev
sf project deploy start --source-dir force-app --target-org mesa-dev
sf org assign permset --name Mesa_Regalos_Admin --target-org mesa-dev
sf apex run --file scripts/apex/crear-campana.apex --target-org mesa-dev   # crea la campaña (repetir cada año)
sf apex run test --test-level RunLocalTests --wait 10 --target-org mesa-dev
sf apex run test --class-names MesaRegalosTest --wait 10 --target-org mesa-dev   # una sola clase
sf apex run test --tests MesaRegalosTest.<metodo> --wait 10 --target-org mesa-dev   # un solo método
# Antes de re-desplegar:
sf project deploy start --dry-run --source-dir force-app --target-org mesa-dev --test-level RunLocalTests --wait 15
```

Scripts en `scripts/apex/` (se ejecutan con `sf apex run --file`): `tokens-faltantes` (rellena `Token_Acceso__c` de miembros creados antes del trigger), `simulador-donativo`, `prueba-paquete`, `limpiar-pruebas` (borra donativos de un contacto de prueba). No hay `package.json`, linter ni tests de LWC en el repo.

## Arquitectura

Todo el acceso a datos del sitio público pasa por **una sola clase Apex** porque el usuario guest no tiene permisos sobre Contact, CampaignMember ni Opportunity (ni debe tenerlos: solo `Enabled Apex Class Access` a `MesaRegalosController`).

- `MesaRegalosController` (`without sharing`, `@AuraEnabled`): único punto de entrada de los LWC. **Cada método nuevo debe validar el token (o los parámetros) antes de tocar datos**, porque se salta el sharing. Contiene validaciones de monto (mín. 100, máx. 1,000,000), paquetes (`P1000`/`P3000`/`P5000`/`Abierto`), campo trampa (`sitioWeb`) y tope de registros por hora (`maxRegistrosPorHora`, `@TestVisible`).
- `MesaRegalosService` (`without sharing`): lógica de negocio compartida. `avanzarEstado` solo mueve el embudo hacia adelante (`ORDEN_ESTADOS`) y no hace nada si la campaña no tiene ese `CampaignMemberStatus`. `confirmarPago` es `@InvocableMethod` idempotente (puede llamarse desde un Flow o un futuro webhook de Stripe) y es lo que crea el `Reconocimiento_Publico__c`.
- Trigger `CampaignMemberToken` (`before insert`): asigna `Token_Acceso__c` (64 hex, `Crypto.generateAesKey(256)`). El token es la **única llave** de los enlaces personales.

Modelo de datos y flujo: Contact + CampaignMember (embudo `Identificado → Contactado → Respondió → Interesado → Redirigido a donar → Donó`) → Opportunity como donativo (`Prospecting` = promesa, `Closed Won` = pagado) → `Reconocimiento_Publico__c` solo si `Visibilidad__c = 'Publica'` **y** hay consentimiento. El muro público lee **únicamente** ese objeto. Donativos de empresa ligan un Account y el reconocimiento sale a nombre de la empresa, sin generación. `Ofrecimiento_Especie__c` es captura interna.

Frontend: LWC `mesaInicio/Registro/Reencuentro/Donacion/Muro` son las páginas del sitio (cada una se arrastra a una página en Experience Builder; Registro y Muro piden el Id de la campaña pegado en el componente). `mesaUtil` (funciones compartidas) y `mesaEstilos`/`mesaAviso` no son páginas.

## Convenciones

- Código, comentarios, etiquetas y mensajes de error están en **español**; mantenerlo.
- Valores de picklist se usan como strings literales (`'Publica'`, `'Closed Won'`, `'Donó'`…) y deben coincidir con los de los campos/estados desplegados.
- `MesaRegalosTest` (346 líneas) cubre controlador y servicio; los despliegues a producción exigen pruebas, así que añadir cobertura con cada método nuevo.
