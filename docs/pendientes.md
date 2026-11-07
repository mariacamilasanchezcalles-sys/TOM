# TOM — Decisiones abiertas, pendientes y errores

> Lista viva (regla 19.15). Se actualiza en cada sesión.

## Decisiones que necesito de María

| # | Decisión | Opciones | Recomendación |
|---|----------|----------|---------------|
| D3 | Crear proyecto de Firebase (plan Spark, sin tarjeta) | Sí / No | Sí, al empezar la Fase 1 |
| D6 | Proveedor de IA (Fase 3) | Workers AI gratuito · Claude API (pago medido) · otros | Decidir con una prueba real; descartar niveles gratuitos que usen tus datos para entrenar |
| D7 | Método de inicio de sesión (Firebase Auth) | Correo y contraseña · Google · Enlace por correo | Correo y contraseña: es el más fiable dentro de una PWA instalada en iOS. Google con *redirect* debe probarse |
| D9 | Recordatorios con la app cerrada | A. Blaze con límites · B. Spark + Worker de Cloudflare sin almacenamiento · C. Sin recordatorios | **A**, con tu aprobación para registrar tarjeta (ver DEC-003) |
| D8 | Escala de importancia y consecuencias | 1–5 · baja/media/alta/crítica | 1–5 con descripciones |

Decisiones ya tomadas: ver [`decisiones.md`](./decisiones.md).

## Verificaciones pendientes en el iPhone (Fase 1)

- [ ] Instalación desde Safari y arranque offline.
- [ ] Persistencia de IndexedDB tras reiniciar el teléfono y tras días sin abrir.
- [ ] `navigator.storage.persist()`: ¿qué devuelve?
- [ ] `speechSynthesis` con voz en español dentro de la PWA instalada.
- [ ] `webkitSpeechRecognition` dentro de la PWA instalada (se espera que falle).
- [ ] Web Push (Fase 2): permiso, llegada con app cerrada, comportamiento con Concentración.
- [ ] Inicio de sesión de Firebase Auth dentro de la PWA instalada.
- [ ] Caché offline de Firestore en Safari/PWA: escritura sin conexión y sincronización al reconectar.
- [ ] Push con FCM en la PWA de iOS (Fase 2).

## Errores conocidos

_(ninguno; aún no hay código)_
