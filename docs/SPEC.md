# SPEC — A Cursar

> **Qué es este documento:** la versión refinada de la SPEC de clase (entregada el 21/10/2026).
> El primer commit del repo es la SPEC de clase **tal cual**; este archivo es su evolución:
> mismos requisitos, ambigüedades resueltas y decisiones incorporadas.
> Cada decisión que no estaba en la SPEC de clase queda acá con su porqué, o con la referencia
> al documento donde se desarrolla (producto → [PRD](PRD.md), técnica → [TRD](TRD.md)).

## 1. Identidad del producto

- **Nombre propio:** A Cursar
- **Una línea:** Para la comunidad de La Plata y alrededores que busca una salida laboral rápida,
  A Cursar reúne en un catálogo validado por la comunidad los cursos gratuitos cortos y medianos
  de cualquier plataforma o institución, y permite a cada persona armar su roadmap de formación.

## 2. Problema

Hoy muchas personas de La Plata y alrededores buscan trabajo y recurren a cursos cortos y medianos
para mejorar su salida laboral. El problema es que la información sobre esos cursos está dispersa
(sitios de instituciones, redes sociales, plataformas online, boca en boca) y suele ser incompleta:
no queda claro si el curso es gratuito, si entrega certificado, si requiere examen o qué
organización lo avala.

Lo que existe hoy no lo resuelve: cada plataforma muestra solo sus propios cursos y los listados
informales (grupos, publicaciones sueltas) no están verificados ni actualizados.

Una comunidad cambia esa ecuación: los propios usuarios aportan los cursos que conocen, declaran
si los tomaron y un moderador valida cada aporte antes de publicarlo. Así se construye un catálogo
único, confiable y local, y cada persona puede planificar su formación con un roadmap personal.

## 3. Visión

En 12 meses, A Cursar es la referencia en la región para elegir un curso gratuito con salida
laboral: el catálogo reúne cientos de cursos validados de plataformas online e instituciones
locales, y quienes buscan capacitarse planifican su formación con su roadmap personal antes de
inscribirse.

## 4. Alcance

### Dentro

- Cuentas de usuario: registro, login con rate limiting, cierre de sesión, recuperación de
  contraseña completa (solicitud → token → nueva contraseña) y contraseñas cifradas con bcrypt.
- Roles: `usuario` y `moderador`, con permisos distintos.
- Aporte de cursos gratuitos de cualquier plataforma o institución, con los campos de la SPEC
  (URL, categoría, tema, título, versión, año, docentes, cuestionarios, examen, certificado, aval)
  más **modalidad** (presencial / semipresencial / virtual), campo agregado por decisión propia.
- Declaración del aportante: indica si tomó o no el curso que aporta.
- Validación de aportes por un moderador: solo los aprobados entran al catálogo; los rechazados
  quedan con un motivo visible para el aportante.
- Catálogo comunitario público y paginado, con búsqueda por texto y filtros por categoría y modalidad.
- Roadmap personal: agregar cursos del catálogo, marcarlos como pendiente / en curso / terminado,
  reordenarlos y quitarlos.
- Interfaz mobile-first con menú sándwich y estética inspirada en Google (Material).

### Fuera (out of scope explícito)

- Cursos pagos, pasarela de pagos o inscripción a cursos desde la app.
- Opiniones, calificaciones, recomendaciones o reseñas de cursos y docentes (versión futura).
- Subida de material de estudio o archivos.
- Emisión de certificados propios de la plataforma.
- Roadmaps públicos o compartidos entre usuarios.
- Edición o baja de un curso ya publicado desde la UI (se resuelve por base de datos en el MVP).
- Login con Google u otras redes sociales.
- App móvil nativa (solo webapp responsive).
- Envío real de emails: en el MVP el enlace de recuperación se emite en el log del backend.

## 5. Reglas de producto clave

| # | Regla | Origen |
|---|-------|--------|
| BR-001 | Un curso aportado entra en estado `en_revision` y solo llega al catálogo comunitario cuando un moderador lo aprueba | SPEC clase ("todo curso aportado y **validado** pasa al catálogo") |
| BR-002 | Quien aporta un curso declara obligatoriamente si lo tomó o no | SPEC clase |
| BR-003 | Cada usuario ve y modifica solo su propio roadmap; el roadmap es privado | decisión propia |
| BR-004 | No puede haber dos cursos con la misma URL (en revisión o publicados) | decisión propia |
| BR-005 | Solo se aceptan cursos gratuitos; el aportante lo confirma al cargarlo | SPEC clase ("cursos gratuitos") |

> El detalle completo de reglas vive en el [PRD §7](PRD.md#7-reglas-de-negocio).

## 6. Stack registrado (versiones exactas con las que se va a desarrollar)

| Capa | Tecnología | Versión exacta instalada |
|------|------------|--------------------------|
| Frontend | React + Vite + pnpm | React 19 (última stable al crear el proyecto) · Vite (fijada en `pnpm-lock.yaml`) · pnpm 10.34.5 · Node v20.20.2 |
| Backend | FastAPI + uv | FastAPI 0.158.2 · uv 0.12.19 · Python 3.13 |
| Base de datos | PostgreSQL | 18.6 (imagen `postgres:18.6`) |
| Infra (dev) | Docker + Docker Compose | Docker 29.9.0 · Docker Compose 5.6.0 |

## 7. Restricciones técnicas heredadas de la SPEC de clase

| # | Restricción | Cómo la vamos a cumplir |
|---|-------------|-------------------------|
| 1 | Mobile-first con mejora progresiva | CSS base sin media queries pensado para 360px; solo `@media (min-width: 768px)` y `(min-width: 1024px)` para agregar columnas y navegación horizontal. Ver [UX-UI §3](UX-UI.md#3-estrategia-mobile-first-con-mejora-progresiva) |
| 2 | Paginación de todos los listados y sus tablas | Paginación por offset (`page`, `page_size`, respuesta con `total`) en catálogo, mis aportes, pendientes de validación y roadmap. Ver [TRD TD-002](TRD.md#2-decisiones-técnicas-mínimo-3) |
| 3 | Menú sándwich en mobile | Componente `Header` con drawer accesible (`aria-expanded`, foco atrapado, cierre con ESC) visible por debajo de 1024px. Ver [UX-UI §4.5](UX-UI.md#45-menú-sándwich) |
| 4 | Screaming Architecture | Carpetas por dominio: `identidad/`, `cursos/`, `aportes/`, `roadmap/` en backend y `features/` equivalentes en frontend. Ver [TRD §3.2](TRD.md#32-estructura-de-carpetas-la-que-se-va-a-crear-en-la-instancia-b) |
| 5 | Apariencia inspirada en Google | Tokens con paleta, tipografía Roboto, radios y sombras de Material. Ver [UX-UI §2](UX-UI.md#2-design-tokens) |

## 8. Vacíos de la SPEC de clase y decisiones tomadas

| # | Vacío detectado en la SPEC | Opciones consideradas | Decisión | Dónde se desarrolla |
|---|----------------------------|-----------------------|----------|---------------------|
| 1 | ¿Quién valida los cursos aportados? | auto-moderación / votos de la comunidad / moderador | **Un usuario con rol moderador** aprueba o rechaza (con motivo). Es simple, auditable y no depende de tener masa crítica de usuarios | PRD BR-001, BR-006 · BackendSchema §2 |
| 2 | ¿Hay roles? | sin roles / usuario + moderador / usuario + moderador + admin | **`usuario` y `moderador`**. El moderador se asigna por seed/base de datos en el MVP | PRD §3 · TRD TD-001 |
| 3 | ¿Qué es "incluir en mi catálogo"? | lista de favoritos / roadmap con estados | **Agregar el curso al roadmap personal** con orden y estado (pendiente / en curso / terminado) | PRD US-005 · TRD TD-004 |
| 4 | ¿La búsqueda es por texto, filtro o ambos? | solo texto / solo filtros / ambos | **Ambos:** texto libre sobre título y tema + filtros por categoría y modalidad | PRD US-001 · BackendSchema §3.2 |
| 5 | ¿Qué otros datos del curso hacen falta? ("y demás datos necesarios", borrador de clase) | ninguno / modalidad / duración, lugar, horario | **Modalidad** (presencial / semipresencial / virtual): clave para una comunidad local | PRD RF-005 · BackendSchema `cursos` |
| 6 | ¿Cómo se envía el enlace de recuperación sin servidor de correo? | SMTP real / servicio externo / log en desarrollo | **Log del backend** en desarrollo; SMTP queda como mejora | TRD TD-006 |
| 7 | ¿Qué valores tiene la lista de categorías? | lista abierta / lista cerrada | **Lista cerrada** cargada por seed (8 categorías orientadas a salida laboral) | BackendSchema `categorias` |

## 9. Historia de la SPEC (autoregistro de cambios)

| Fecha | Cambio | Por qué |
|-------|--------|---------|
| 20/10/2026 | SPEC de clase redactada en el aula | — |
| 21/10/2026 | SPEC de clase formalizada y entregada por el docente | — |
| 26/10/2026 | Primer commit: SPEC de clase tal cual | regla de la consigna |
| 26/10/2026 | Refinamiento: identidad (A Cursar), problema, visión, alcance, reglas, stack registrado, restricciones y vacíos (§1–§8) | resolver ambigüedades antes de construir |
