# TOM — Análisis de Fase 0

> Estado: **propuesta para revisión de María**. Nada de este documento es definitivo hasta que se apruebe.
> Fecha: 2026-11-07. Las cifras de servicios gratuitos deben volver a verificarse al momento de adoptarlos.

---

## 1. Resumen ejecutivo

1. **La visión es viable, pero no toda en una PWA de iOS.** Agenda, tareas, priorización, memoria, respaldos, IA y notificaciones push son viables y gratuitas (o casi). Lo que **no** es viable como lo describe el documento: bloquear TikTok/YouTube desde una PWA, una palabra activadora «Hola, Tom» con la app cerrada, y (probablemente) el reconocimiento de voz dentro de la PWA instalada.
2. **La proactividad obliga a tener servidor desde temprano.** Una PWA en iOS no puede ejecutar código en segundo plano ni programar notificaciones locales. Para que TOM te recuerde algo a las 9:00 cuando la app está cerrada, *un servidor* tiene que saber que existe ese recordatorio y enviarte un push. Esto cambia el orden de las fases propuesto.
3. **El reparto IA / motor determinista es la mejor decisión del documento** y la mantengo como eje de la arquitectura. Añado una regla: el motor determinista debe escribirse **una sola vez** en TypeScript puro y ejecutarse tanto en el teléfono (offline) como en el servidor (recordatorios).
4. **Recomiendo reordenar las fases:** primero un núcleo local útil, después servidor + respaldo + recordatorios, y *después* la IA. Sin recordatorios fiables y sin datos a salvo, la IA no aporta lo suficiente.
5. **Riesgo principal: pérdida de datos**, no la falta de inteligencia. Safari puede borrar datos locales; los respaldos son obligatorios desde la primera versión.

---

## 2. Requisitos

### 2.1 Funcionales (resumen agrupado)

| ID | Área | Requisito |
|----|------|-----------|
| RF-01 | Estructura | Áreas (9 iniciales) → proyectos → objetivos → tareas/compromisos. |
| RF-02 | Tareas | Ficha completa (nombre, descripción, área, proyecto, objetivo, importancia, fecha límite, fecha propuesta, duración est./mín./máx., energía, dificultad, consecuencias, dependencias, estado, historial). 7 estados. |
| RF-03 | Fechas | Distinguir fecha límite, fecha propuesta, horario de ejecución y fecha de creación. Una fecha propuesta nunca se convierte sola en fecha límite. |
| RF-04 | Planificación | Proponer fecha para tareas sin fecha, con explicación, y pedir confirmación. |
| RF-05 | Agenda | Construir el plan del día/semana respetando compromisos, energía, descansos, comidas y márgenes. |
| RF-06 | Priorización | Puntuación ponderada configurable + reglas de prioridad especial. |
| RF-07 | Reorganización | Recalcular ante cambios, mostrar conflictos y alternativas; **no aplicar cambios importantes sin aprobación**. |
| RF-08 | Consultas | «¿Tengo 5 minutos?», «¿qué llevo posponiendo?», «¿qué hago primero?», etc. |
| RF-09 | Energía | Registro diario 1–5; la energía declarada prevalece; sin respuesta → neutral marcado como «desconocido». |
| RF-10 | Aprendizaje de tiempos | Registrar tiempo real vs. estimado y ajustar estimaciones y división de tareas. |
| RF-11 | Rituales | Bienvenida breve + energía; revisión del día bajo demanda; cierre nocturno. |
| RF-12 | Recordatorios | 5 niveles de insistencia, frecuencia configurable, se detiene tras el 5.º. Estados: enviado / entregado / visto (si comprobable) / respondido / iniciado / terminado. |
| RF-13 | Concentración | Modo concentración con salida de emergencia, dentro de lo que permita iOS. |
| RF-14 | Memoria | Memoria estructurada por categorías, con tipo epistémico (hecho / preferencia / patrón / inferencia) y evidencia. |
| RF-15 | Decisiones | «TOM, queda establecido» registra una decisión cerrada que TOM no vuelve a discutir salvo información nueva o riesgo. |
| RF-16 | Respaldo | Exportar e importar toda la información. |
| RF-17 | IA | Lenguaje natural → herramientas controladas; la IA nunca escribe directamente en la base de datos. |
| RF-18 | Permisos | Niveles 0–3 de autonomía; registro de operaciones y aprobaciones. |
| RF-19 | Voz | Entrada y salida por voz; seguimiento de conversación. |
| RF-20 | Finanzas | Ingresos, gastos, cuentas, pagos pendientes, cortes, presupuestos, alertas. (Fase posterior.) |
| RF-21 | Investigación | Buscar información cuando sea necesario. (Fase posterior; ver riesgos de seguridad.) |

### 2.2 No funcionales

| ID | Requisito |
|----|-----------|
| RNF-01 | Costo objetivo $0 MXN; techo ~$200 MXN/mes solo con aprobación explícita. Nada que genere cargos automáticos. |
| RNF-02 | Funciona en iPhone 12 mini, iOS 26.x, instalada desde Safari. Sin cuenta de Apple Developer ni computadora. |
| RNF-03 | Funciones esenciales offline. |
| RNF-04 | Repositorio privado. |
| RNF-05 | Sin claves API en el cliente. Sin números de tarjeta, contraseñas ni credenciales bancarias. |
| RNF-06 | Datos financieros separados del contexto general enviado a la IA. |
| RNF-07 | Honestidad verificable: TOM solo afirma acciones que el motor confirma. |
| RNF-08 | Código modular, tipado, probado, documentado; continuable desde otro dispositivo o sesión. |
| RNF-09 | Legibilidad por encima de la decoración (pantalla de 5,4"). |
| RNF-10 | Español natural; personalidad definida (profesional, con criterio, humor ocasional). |
| RNF-11 | Proveedor de IA intercambiable. |

---

## 3. Confirmado vs. propuesto vs. pendiente

**Confirmado por María (lo trato como requisito):**
- PWA instalada desde Safari como primera versión.
- Repositorio privado en GitHub. Costo $0, techo $200 MXN/mes con aprobación.
- Separación IA / motor determinista, con herramientas controladas.
- Autoridad final de la usuaria y la frase «TOM, queda establecido».
- Niveles de autonomía y lista de acciones que requieren autorización.
- Las 9 áreas iniciales, los 7 estados de tarea, la escala de energía 1–5.
- Memoria estructurada (no conversaciones completas) con distinción hecho/preferencia/patrón/inferencia.
- Respaldos exportables. Desarrollo progresivo por fases.

**Propuesto, no confirmado (se decide con evidencia):**
- React + TypeScript (lo recomiendo; ver §6).
- IndexedDB + Service Worker (inevitables en una PWA; la librería es decisión abierta).
- Cloudflare Workers + D1 (lo recomiendo; ver §6).
- Pesos de priorización 25/25/20/10/10/10 (hipótesis inicial).
- Frecuencia de recordatorio de 1 hora (punto de partida de prueba).
- Proveedor de IA concreto.
- Orden de fases (propongo cambiarlo; ver §8).

**Pendiente de decidir** → ver [`pendientes.md`](./pendientes.md).

---

## 4. Contradicciones y requisitos inviables

### 4.1 Inviables o no fiables en una PWA de iOS

| Requisito | Realidad | Alternativa realista |
|-----------|----------|----------------------|
| Bloquear TikTok / Facebook / YouTube | Solo es posible con la API Screen Time (FamilyControls/ManagedSettings), que requiere **app nativa** y un *entitlement* que Apple concede a solicitud, con cuenta de desarrollador de pago. Una PWA no puede. | **Atajos de iOS (gratis):** automatización personal «Cuando se abra TikTok» → consulta a TOM si hay modo concentración activo → si lo hay, abre TOM y muestra la tarea en curso. Es **fricción, no bloqueo**: puedes desactivar la automatización. Complementar con *Tiempo en pantalla* y modos de Concentración configurados por ti. La «salida de emergencia» es simplemente desactivar el modo en TOM. |
| «Hola, Tom» con la app cerrada | Ninguna app de terceros (ni siquiera nativa) puede tener escucha permanente con palabra personalizada en iOS. Una PWA ni siquiera corre en segundo plano. | «**Oye Siri, TOM**» → Atajo que dicta tu frase, la envía al servidor de TOM y lee la respuesta en voz alta. Funciona sin abrir la app. Gratis. |
| Reconocimiento de voz dentro de la PWA instalada | `webkitSpeechRecognition` existe en Safari, pero los reportes de WebKit y de desarrolladores indican que **no funciona en apps añadidas a la pantalla de inicio**. No encontré evidencia de que iOS 26 lo haya corregido. **Hay que probarlo en tu iPhone.** | (a) Dictado del teclado de iOS (botón de micrófono del teclado: funciona en cualquier campo de texto, gratis, buena calidad en español); (b) grabar audio y transcribir en el servidor (cuesta o consume cuota gratuita); (c) el Atajo de Siri. La **salida** por voz (`speechSynthesis`) sí suele funcionar; también hay que verificarla. |
| Notificaciones programadas locales | Las PWAs de iOS no pueden programar notificaciones locales ni ejecutar tareas en segundo plano. | Web Push (soportado en PWAs instaladas desde iOS 16.4) enviado **desde un servidor** con tarea programada (cron). |
| Saber si una notificación fue vista | iOS no lo informa. Solo se puede saber si la **tocaste** (evento `notificationclick`). «Entregada» se puede aproximar si el Service Worker reporta la recepción, pero no es garantizable. | Modelar los estados honestamente: `enviada` (seguro), `recibida` (probable), `abierta` (seguro si tocas), `vista` → **no medible**, no se usa. |
| Romper modos de Concentración / No molestar | Las notificaciones web no pueden marcarse como críticas para atravesar «No molestar» (verificar en dispositivo si «sensibles al tiempo» aplica). | Configurar TOM como app permitida en tus modos de Concentración. |
| «Al despertar» | TOM no puede saber cuándo despiertas. | Opción A: la primera vez que abres TOM en el día. Opción B: Atajo «Cuando se detenga la alarma» → abrir TOM. Opción C: push a hora fija. Recomiendo A + B. |

### 4.2 Contradicciones internas del documento

1. **«Offline y datos locales» vs. «proactiva y persistente».** Lo proactivo (recordatorios, insistencia, cierre nocturno) requiere que el servidor conozca tus tareas y horarios. **Resolución:** arquitectura *local-first con sincronización*: el teléfono trabaja offline y el servidor guarda una copia para respaldo y recordatorios. Implica confiar tus datos a Cloudflare (ver §7).
2. **«Comunicación principalmente por voz» vs. plataforma elegida.** En una PWA de iOS la voz será, en la práctica, dictado del teclado + lectura en voz alta, o Siri vía Atajos. La «conversación natural con interrupciones» es una meta de fase avanzada, no del inicio.
3. **«Finanzas separadas de la IA» vs. «advertirme antes de gastar».** No es contradicción si el **motor determinista** calcula las alertas (85 % del presupuesto, pagos pendientes) y la IA solo recibe agregados cuando hace falta. Así lo diseño.
4. **«No publicar el código» vs. «PWA».** El repositorio puede ser privado, pero el JavaScript que corre en tu iPhone es descargable por cualquiera que abra la URL. No es un problema si **no hay secretos en el cliente** y la API exige autenticación. Además, el sitio se protegerá con login para que nadie más lo use.
5. **«Aprender mis tiempos» vs. registro.** No se puede aprender sin datos reales. Desde la Fase 1 hay que registrar inicio/fin real de las tareas (cronómetro de un toque), aunque el «aprendizaje» llegue después.
6. **«Proteger el descanso» vs. «insistencia de 5 niveles».** Los recordatorios deben respetar horarios de silencio (sueño, comidas, tiempo libre protegido) salvo tareas marcadas como críticas.
7. **Pesos de priorización con precisión falsa.** Seis factores con porcentajes exactos dan una sensación de rigor que los datos de entrada (importancia y consecuencias subjetivas) no tienen. Propongo: primero **restricciones duras** (compromisos fijos, fechas límite que no caben, dependencias), luego la puntuación solo para ordenar lo flexible, y siempre mostrar el **porqué** en lenguaje claro.
8. **«JARVIS» vs. «sistema pequeño que funcione».** Comparto tu propia conclusión: la estética futurista es una capa visual; el valor está en la agenda fiable. Lo visual se mantiene sobrio para que la pantalla de 5,4" sea legible.

---

## 5. Revisión de la arquitectura propuesta

| Elemento | Veredicto | Comentario |
|----------|-----------|------------|
| React | ✅ Aceptar | No es lo más ligero (Preact/Svelte lo son), pero es lo más documentado y lo que mejor escriben los asistentes de IA, y facilita una futura app nativa con React Native/Expo. |
| TypeScript | ✅ Aceptar | Indispensable para un modelo de datos de este tamaño. |
| PWA | ✅ Aceptar | Única opción sin cuenta de Apple ni computadora. |
| IndexedDB | ✅ Aceptar con matices | No es almacenamiento seguro ni permanente. Usar `navigator.storage.persist()` (sin garantía), respaldo exportable y copia en servidor. Las apps en pantalla de inicio están exentas del borrado de 7 días de Safari, pero no del borrado por falta de espacio. |
| Service Worker | ✅ Aceptar | Necesario para offline y push. |
| GitHub | ✅ Aceptar | Repositorio privado, Actions para pruebas. |
| Cloudflare Workers | ✅ Recomendado | Plan gratuito por defecto, sin tarjeta; incluye *Cron Triggers* (5 por cuenta en plan gratuito: suficiente, usaremos 1–2). Hospeda también la PWA (repos privados soportados). GitHub Pages con repo privado requiere plan de pago. |
| Cloudflare D1 | ✅ Recomendado, **adelantado** | No lo dejaría para la Fase 5: hace falta para respaldo y recordatorios. Desde sept. 2026 las consultas fallan (no se cobran) si se excede el límite diario gratuito: bueno para el presupuesto; el uso de una persona queda muy por debajo. |
| Proveedor de IA intercambiable | ✅ Aceptar | Implementarlo como una interfaz `ProveedorIA` en el servidor. Detalles en §6.4. |

---

## 6. Arquitectura inicial propuesta

### 6.1 Diagrama

```
 iPhone (PWA instalada)                         Cloudflare (plan gratuito)
┌──────────────────────────────┐              ┌────────────────────────────────┐
│ UI React (orbe, agenda, ...) │              │ Worker "tom-api"               │
│        │                     │   HTTPS +    │  ├─ /sync  (operaciones)       │
│ Capa de herramientas         │◄────────────►│  ├─ /asistente (IA, Fase 3)    │
│  (crear_tarea, ...)          │   auth       │  ├─ /push  (suscripciones)     │
│        │                     │              │  └─ cron: recordatorios        │
│ @tom/core (motor determinista)│◄── mismo ───►│ @tom/core (mismo código)       │
│        │                     │   código     │        │                       │
│ IndexedDB (copia local)      │              │ D1 (copia del servidor)        │
│ Service Worker (offline/push)│              │ Secretos: claves IA, VAPID     │
└──────────────────────────────┘              └────────────────────────────────┘
        ▲                                                   │
        │ Atajos de iOS (Siri, apertura de apps, alarma) ───┘
```

### 6.2 Estructura del repositorio (monorepo pequeño)

```
TOM/
├─ docs/                 ← análisis, decisiones (ADR), pendientes
├─ packages/core/        ← motor determinista: modelos, validación, priorización,
│                          planificador, permisos. TypeScript puro, SIN dependencias
│                          de navegador ni de servidor. 100 % probado con Vitest.
├─ apps/web/             ← PWA (React + Vite). Fase 1.
└─ apps/api/             ← Worker de Cloudflare. Fase 2.
```

Regla: **toda regla de negocio vive en `core`.** La PWA y el Worker solo la llaman (cumple la regla 6: no duplicar lógica).

### 6.3 Dependencias mínimas propuestas (Fase 1)

| Dependencia | Problema que resuelve | ¿Necesaria? |
|-------------|----------------------|-------------|
| Vite | Compilar y servir la app | Sí |
| React + react-dom | Interfaz | Sí |
| vite-plugin-pwa (Workbox) | Generar manifest y Service Worker correctos | Sí; hacerlo a mano es propenso a errores de caché |
| Dexie | IndexedDB con consultas e **migraciones de esquema** versionadas | Sí; la API nativa es muy verbosa y las migraciones son críticas |
| Vitest | Pruebas del motor | Sí |
| Playwright | Pruebas de extremo a extremo en CI con motor WebKit | Sí, pero solo en CI; **no sustituye** probar en el iPhone real |

Sin librería de estado global, sin librería de componentes, sin CSS framework al inicio. Se añaden solo si aparece un problema concreto.

### 6.4 Inteligencia artificial (Fase 3)

- La IA vive **solo en el Worker**; el teléfono nunca ve la clave.
- Patrón: la IA recibe un contexto mínimo + definiciones de herramientas (JSON Schema) → propone llamadas → **el motor valida** (parámetros, permisos, nivel de autonomía) → ejecuta o pide aprobación → devuelve el resultado real. TOM solo dice «hecho» si el motor devolvió éxito.
- **Proveedores a comparar** (decisión abierta, con prueba real en español):
  - *Cloudflare Workers AI* (cuota gratuita diaria; modelos abiertos; calidad en español y en uso de herramientas a verificar).
  - *Gemini API gratuita*: **no la recomiendo para datos personales**: en el nivel gratuito Google puede usar tus mensajes para mejorar productos y revisores humanos pueden leerlos.
  - *Claude (Anthropic) API*: de pago por uso, sin nivel gratuito. Muy buena en razonamiento, español y herramientas. Un modelo pequeño (tipo Haiku) para el día a día y uno grande solo bajo demanda podría caber en el techo de $200 MXN/mes, **pero hay que medirlo con tu uso real** antes de aprobarlo. Nota: la suscripción a Claude (app) **no** incluye la API.
- Límite de gasto duro en el Worker (tokens/día) para que un error nunca genere una factura.

### 6.5 Autenticación

TOM es de una sola usuaria. Opciones:
1. **Cloudflare Access** (gratis para pocos usuarios, login con código por correo) delante del sitio y de la API. Mínimo código. Riesgo: el flujo de login dentro de una PWA instalada en iOS puede ser incómodo → **probar en el iPhone**.
2. **Passkey (Face ID) propia** en el Worker. Mejor experiencia, más código.

Recomiendo probar (1) primero y pasar a (2) solo si la experiencia es mala.

### 6.6 Modelo de datos (borrador)

Todas las entidades llevan `id` (UUID generado en el cliente, para sincronizar), `creadoEn`, `actualizadoEn`, `eliminadoEn?` (borrado lógico).

- `Area`, `Proyecto`, `Objetivo`
- `Tarea` (campos de RF-02; `fechaLimite`, `fechaPropuesta` + `fechaPropuestaConfirmada: boolean`, `duracionEstimada/Min/Max`, `energiaRequerida 1–5`, `importancia`, `consecuencias {gravedad, descripcion}`, `dependeDe[]`, `estado`)
- `EventoTarea` (historial: cambio de estado, reprogramación, con motivo y autor: usuaria / TOM-aprobado / sistema)
- `Compromiso` (bloque fijo: cita, trabajo, clase)
- `BloqueProtegido` (sueño, comidas, descanso, transiciones; reglas recurrentes)
- `PropuestaAgenda` + `BloqueAgenda` (lo planificado; una propuesta no es la agenda vigente hasta aprobarse)
- `SesionTrabajo` (inicio/fin real → base para aprender tiempos)
- `RegistroEnergia` (fecha, nivel, `origen: declarada | provisional`)
- `Recordatorio` + `IntentoRecordatorio` (nivel 1–5, estados honestos de §4.1)
- `Decision` (tema, contenido, argumentos, `estado: abierta | establecida`, fecha) ← «queda establecido»
- `Memoria` (categoría, `tipo: hecho | preferencia | patron | inferencia`, `evidencias[]`, `confianza`, `vigente`)
- `Operacion` (registro de auditoría: quién, qué, nivel de permiso, aprobación)
- `Configuracion` (pesos de prioridad, frecuencias, horarios de silencio, zona horaria)
- *Finanzas (Fase 5):* almacén separado.

Fechas: ISO 8601 con zona horaria explícita. Duraciones en minutos enteros.

### 6.7 Planificador (motor determinista, v1)

1. Construir la línea del día: compromisos fijos → bloques protegidos → márgenes entre bloques.
2. Detectar conflictos duros (fechas límite que ya no caben, dependencias sin resolver).
3. Calcular la puntuación de las tareas flexibles (pesos configurables).
4. Rellenar huecos con un algoritmo voraz que combina puntuación y compatibilidad de energía, dejando un porcentaje del tiempo libre sin asignar.
5. Producir una **propuesta con razones** («la programo el jueves porque vence el viernes y el miércoles ya tienes 6 h ocupadas»). Las razones las genera el motor, no la IA: así TOM explica incluso sin conexión.

No usaremos optimizadores complejos hasta que el voraz se quede corto con datos reales.

---

## 7. Riesgos técnicos y de seguridad

| # | Riesgo | Prob. | Impacto | Mitigación |
|---|--------|-------|---------|------------|
| R1 | Pérdida de datos (Safari borra IndexedDB, cambio de teléfono) | Media | Alto | `persist()`, exportación JSON desde Fase 1, aviso si hace >7 días del último respaldo, copia en D1 desde Fase 2. |
| R2 | Voz no funciona en la PWA instalada | Alta | Medio | Prueba en el dispositivo en Fase 1; alternativas: dictado del teclado y Atajos de Siri. |
| R3 | Push no llega o llega tarde (Concentración, ahorro de batería) | Media | Alto | Probar en el dispositivo; estados honestos; TOM como app permitida en Concentración. |
| R4 | La IA afirma acciones no ejecutadas | Media | Alto | Solo el motor confirma acciones; la UI muestra el registro de operaciones. |
| R5 | Inyección de instrucciones (prompt injection) al investigar en la web | Media | Alto | Contenido externo marcado como no confiable; herramientas sensibles requieren aprobación aunque la IA las pida. |
| R6 | Costo inesperado | Baja | Alto | Nunca activar facturación sin tu aprobación; límite diario en el Worker; preferir servicios que **fallen** en vez de cobrar. |
| R7 | Cambian los niveles gratuitos | Media | Medio | Proveedores intercambiables; datos exportables; nada atado a una sola API propietaria. |
| R8 | Datos personales en terceros (Cloudflare, proveedor de IA) | Cierta | Medio | Contexto mínimo a la IA; finanzas solo agregadas; no usar niveles gratuitos que entrenen con tus datos. Cifrado de extremo a extremo **no es compatible** con recordatorios e IA en servidor: hay que aceptarlo conscientemente. |
| R9 | Acceso de terceros a la URL | Media | Alto | Autenticación obligatoria en sitio y API. |
| R10 | Crecimiento descontrolado del alcance | Alta | Alto | Fases con criterios de aceptación; nada nuevo sin aprobación. |
| R11 | Dependencia de sesiones de IA para mantener el código | Alta | Medio | Documentación en `docs/`, decisiones registradas, pruebas automáticas que detectan regresiones. |
| R12 | Zona horaria y horario de verano mal manejados | Media | Medio | Zona horaria explícita en configuración y pruebas específicas. |

---

## 8. Plan por etapas (orden recomendado)

> Cambio respecto a tu propuesta: **servidor + recordatorios antes que IA**, y **finanzas antes que voz avanzada**. Motivo: lo que hace útil a TOM cada día es que la agenda sea fiable, que te avise y que tus datos no se pierdan. La IA multiplica el valor de eso, pero no lo sustituye.

### Fase 0 — Diseño (este documento)
- Criterio de salida: apruebas el análisis y respondes las decisiones abiertas de `pendientes.md`.

### Fase 1 — Núcleo local usable (sin servidor, sin IA)
- Configuración del proyecto, CI con pruebas, despliegue gratuito con vista previa por rama.
- `core`: modelo de datos, validaciones, estados, priorización, planificador v1 con razones.
- PWA: captura rápida de tareas, compromisos, áreas/proyectos, vista «Hoy» con casillas, cronómetro de un toque, energía al primer uso del día, propuesta de agenda con aprobar/rechazar, consultas de huecos libres (sin lenguaje natural: botones).
- Exportar/importar respaldo JSON. Funcionamiento offline.
- **Pruebas en el iPhone:** instalación, offline, persistencia tras reinicio, `speechSynthesis`, `webkitSpeechRecognition`.
- Criterios de aceptación: puedes planificar y cerrar un día real sin conexión; un respaldo exportado se restaura sin pérdidas; las pruebas del motor pasan en CI.

### Fase 2 — Servidor, sincronización y recordatorios
- Worker + D1, autenticación, sincronización por cola de operaciones (una sola usuaria → «la última escritura gana» por registro es suficiente).
- Web Push con cron; recordatorios de 5 niveles con horarios de silencio; estados honestos.
- Atajos de iOS: modo concentración (fricción al abrir apps), alarma → abrir TOM.
- Cierre nocturno básico (determinista).
- Aceptación: recibes recordatorios con la app cerrada durante una semana; reinstalar la PWA recupera los datos desde el servidor.

### Fase 3 — Asistente inteligente
- Prueba comparativa de proveedores con tus propios ejemplos; decisión de costo contigo.
- Herramientas controladas, niveles de autonomía, registro de operaciones.
- Memoria estructurada con evidencias; decisiones «queda establecido».
- Personalidad y tono.
- Atajo «Oye Siri, TOM».
- Aceptación: conjunto de casos de prueba («crea…», «¿qué llevo posponiendo?», intento de acción no autorizada) con resultados correctos y sin acciones inventadas.

### Fase 4 — Seguimiento y aprendizaje
- Aprendizaje de tiempos (estadística simple: razón real/estimado por tipo de tarea), división adaptativa, detección de postergación con evidencia, seguimiento de objetivos, revisión semanal.

### Fase 5 — Administración personal
- Finanzas, presupuestos, pagos pendientes, compras/supermercado, alertas deterministas.

### Fase 6 — Expansión
- Interfaz de computadora (la misma PWA ya funcionará en navegador de escritorio), integraciones (calendario), voz conversacional avanzada, y evaluar app nativa con Expo (requiere Apple Developer: aprox. 99 USD/año; Screen Time requiere además aprobación de Apple).

---

## 9. Evaluación de tecnologías sugeridas por terceros

| Tecnología | ¿Qué resuelve? | Veredicto para TOM |
|------------|----------------|--------------------|
| **Ollama** | Ejecutar modelos de IA en tu propio hardware | **No aplica ahora.** Requiere una computadora con RAM/GPU suficiente; el iPhone 12 mini no sirve y no tienes computadora. Reconsiderar si algún día tienes una y quieres privacidad total. |
| **Superpowers** | Colección de *skills* y metodología para agentes de programación (planificar, TDD, revisiones) | **Opcional.** Adoptamos sus prácticas (plan antes de código, pruebas primero, revisión) sin depender de la herramienta. Se puede instalar más adelante si aporta. |
| **Playwright** | Automatizar y probar el navegador | **Sí, en CI** (Fase 1), con motor WebKit. No reproduce exactamente iOS ni el modo instalado: siempre habrá pruebas manuales en tu iPhone. |
| **MCP** | Protocolo para que modelos usen herramientas y datos externos | **Más adelante.** Diseñaremos las herramientas de TOM con JSON Schema, de modo que exponerlas como servidor MCP (p. ej., para usar TOM desde la app de Claude) sea un adaptador fino. No es necesario en Fase 1–2. |
| **Claude Opus** | Modelo grande de razonamiento | **Útil para desarrollar TOM** (lo que hacemos ahora). Como cerebro diario de TOM es caro para el presupuesto; mejor un modelo pequeño por defecto y uno grande solo bajo demanda, tras medir costos. |
| **Expo** | Apps nativas con React Native, compilación en la nube | **Fase 6.** Es el camino natural si algún día quieres capacidades nativas (Screen Time, voz en segundo plano limitada). Requiere cuenta de Apple Developer para instalar de forma estable. |

---

## 10. Cómo trabajaremos sin computadora

- **Tú, desde el iPhone:** revisar y aprobar en GitHub (app o Safari), abrir sesiones de Claude Code en la web/app, crear la cuenta gratuita de Cloudflare y conectar el repositorio (Safari), probar cada versión desde la URL de vista previa, instalarla en la pantalla de inicio, crear los Atajos.
- **Yo, en sesiones en la nube:** escribir código y pruebas, ejecutar pruebas, hacer *commits* en ramas.
- **GitHub Actions:** ejecuta las pruebas en cada cambio (los repos privados tienen minutos gratuitos mensuales; nuestro uso es pequeño).
- **Cloudflare:** compila y publica automáticamente cada rama en una URL de vista previa.
- Nada de esto requiere computadora. Cuando tengas una, el flujo es idéntico.

---

## Fuentes consultadas

- Límites de Cloudflare Workers: https://developers.cloudflare.com/workers/platform/limits
- Precios de Cloudflare Workers: https://developers.cloudflare.com/workers/platform/pricing/
- D1: aplicación de límites del plan gratuito (sept. 2026): https://developers.cloudflare.com/changelog/post/2026-09-01-d1-free-tier-limit-enforcement/
- WebKit Bug 225298 (SpeechRecognition no disponible en web apps de pantalla de inicio): https://bugs.webkit.org/show_bug.cgi?id=225298
- Foro de Apple, webkitSpeechRecognition en PWA: https://developer.apple.com/forums/thread/748048
- Política de datos de la API de Gemini: https://ai.google.dev/gemini-api/docs/logs-policy
- Análisis de términos de Gemini (Simon Willison, 2024): https://simonwillison.net/2024/Oct/17/gemini-terms-of-service
