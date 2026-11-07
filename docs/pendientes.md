# TOM — Decisiones abiertas, pendientes y errores

> Lista viva (regla 19.15). Se actualiza en cada sesión.

## Decisiones que necesito de María

| # | Decisión | Opciones | Recomendación |
|---|----------|----------|---------------|
| D1 | Orden de fases | (a) Original: IA en Fase 2 · (b) Servidor + recordatorios antes que IA | **(b)**: ver `00-analisis-fase0.md` §8 |
| D2 | Dónde viven tus datos | (a) Solo en el teléfono (sin recordatorios con la app cerrada) · (b) Teléfono + copia en Cloudflare | **(b)**, aceptando que Cloudflare custodia los datos |
| D3 | Crear cuenta gratuita de Cloudflare | Sí / No | Sí, **sin** registrar tarjeta |
| D4 | Zona horaria / ciudad | — | Necesaria para agenda y recordatorios |
| D5 | Bloques protegidos iniciales | Horario de sueño, comidas, tiempo libre mínimo | Que los definas tú; no los invento |
| D6 | Proveedor de IA (Fase 3) | Workers AI gratuito · Claude API (pago medido) · otros | Decidir con una prueba real; descartar niveles gratuitos que usen tus datos para entrenar |
| D7 | Autenticación | Cloudflare Access · Passkey propia | Probar Access primero |
| D8 | Escala de importancia y consecuencias | 1–5 · baja/media/alta/crítica | 1–5 con descripciones |

## Verificaciones pendientes en el iPhone (Fase 1)

- [ ] Instalación desde Safari y arranque offline.
- [ ] Persistencia de IndexedDB tras reiniciar el teléfono y tras días sin abrir.
- [ ] `navigator.storage.persist()`: ¿qué devuelve?
- [ ] `speechSynthesis` con voz en español dentro de la PWA instalada.
- [ ] `webkitSpeechRecognition` dentro de la PWA instalada (se espera que falle).
- [ ] Web Push (Fase 2): permiso, llegada con app cerrada, comportamiento con Concentración.
- [ ] Login de Cloudflare Access dentro de la PWA instalada (Fase 2).

## Errores conocidos

_(ninguno; aún no hay código)_
