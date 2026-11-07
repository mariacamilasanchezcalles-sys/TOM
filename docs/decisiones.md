# TOM — Registro de decisiones

> Cada decisión: fecha, estado, contexto, decisión y consecuencias.
> Estados: **aprobada** · **propuesta** · **abierta** · **reemplazada**.

## DEC-001 — Orden de fases
- Fecha: 2026-11-07 · Estado: **aprobada** por María.
- Decisión: servidor, sincronización y recordatorios (Fase 2) **antes** que la IA (Fase 3). Orden completo en `00-analisis-fase0.md` §8.

## DEC-002 — Backend: Firebase (Google) en lugar de Cloudflare
- Fecha: 2026-11-07 · Estado: **aprobada** por María (reemplaza la propuesta de Cloudflare Workers + D1 de `00-analisis-fase0.md` §5–6).
- Usaremos:
  - **Firestore**: base de datos en la nube. Su SDK web trae caché offline en IndexedDB y cola de escrituras pendientes, así que **no** hace falta Dexie ni un sistema de sincronización propio.
  - **Firebase Authentication**: inicio de sesión.
  - **Firebase Hosting**: publicar la PWA.
  - **Firebase Cloud Messaging (FCM)**: notificaciones push a la PWA.
- Consecuencias:
  - Google custodia los datos (la misma cuestión de confianza que con Cloudflare).
  - La configuración web de Firebase (`apiKey`, `projectId`…) **sí** va en el cliente: no es un secreto, solo identifica el proyecto. La seguridad depende de **Firebase Auth + Reglas de seguridad de Firestore**, que deben permitir acceso solo a la cuenta de María y tener pruebas automáticas.
  - Las reglas de negocio siguen en `packages/core` (TypeScript puro), independientes de Firebase.
  - Riesgo de dependencia de un proveedor: se mitiga con el respaldo JSON y con una capa de acceso a datos (adaptador) separada del motor.

## DEC-003 — Cómo enviar recordatorios con la app cerrada
- Fecha: 2026-11-07 · Estado: **abierta** (ver `pendientes.md` D9).
- Problema: el plan gratuito de Firebase (Spark) **no permite funciones programadas**: dependen de Cloud Scheduler y Pub/Sub, que requieren el plan Blaze con cuenta de facturación (tarjeta).
- Opciones:
  - **A. Plan Blaze con límites**: funciones programadas en Firebase. Requiere registrar tarjeta. Con el uso de una sola persona se espera **$0**, porque se usan las cuotas gratuitas. Firebase permite un *límite de gasto* que pausa Cloud Functions al llegar al tope, pero **no existe un límite equivalente para Firestore**: solo una alerta, que no detiene el cobro. El riesgo real es un error de programación que haga lecturas o escrituras en bucle.
  - **B. Spark (sin tarjeta) + Cloudflare Worker gratuito sin almacenamiento**: el Worker solo se despierta cada pocos minutos, lee en Firestore los recordatorios pendientes y envía el push. Cloudflare no guarda datos, pero los ve pasar.
  - **C. Spark sin servidor**: no hay recordatorios con la app cerrada. Contradice el objetivo de la Fase 2.
- Recomendación: **A**, con límite de gasto de $0–50 MXN en Functions, alerta de presupuesto, límites de frecuencia en el código y pruebas. Requiere la aprobación explícita de María para registrar la tarjeta (regla del presupuesto, §13).
- Esto no bloquea la Fase 1: Firestore, Auth y Hosting funcionan en Spark sin tarjeta.

## DEC-004 — Zona horaria
- Fecha: 2026-11-07 · Estado: **aprobada**.
- San Luis Potosí, México → zona IANA `America/Mexico_City` (UTC−6 todo el año; México eliminó el horario de verano en 2022, salvo la franja fronteriza).
- Se guarda como configuración editable, no como constante en el código.

## DEC-005 — Sueño y comidas ajustables
- Fecha: 2026-11-07 · Estado: **aprobada**.
- No habrá horarios predeterminados inventados. Diseño:
  1. **Plantilla semanal** opcional (p. ej., entre semana / fin de semana) que María define y edita.
  2. **Ajuste del día** («hoy me levanté a las 10», «hoy como a las 4») que reemplaza la plantilla solo ese día.
  3. Si no hay datos, TOM **pregunta** en lugar de suponer, y no programa recordatorios en horarios desconocidos que podrían ser de sueño.
- El planificador trata estos bloques como protegidos: solo se mueven con aprobación.
