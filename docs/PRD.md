# PRD — Product Requirements Document — A Cursar

> **Qué es:** el QUÉ y el POR QUÉ: para quién construimos, qué problema resuelve, qué hace y qué
> **no** hace. La tecnología vive en el [TRD](TRD.md).
> **Cuándo está bien:** alguien del equipo puede construir la feature correcta sin preguntar nada de
> producto; el "no" está tan claro como el "sí"; cada historia tiene criterios verificables.

## 1. Resumen ejecutivo

A Cursar es una webapp comunitaria para la región de La Plata donde las personas que buscan
capacitarse para conseguir trabajo encuentran cursos gratuitos cortos y medianos en un solo lugar.
Los usuarios aportan cursos de cualquier plataforma o institución, un moderador los valida y los
aprobados pasan a un catálogo público y paginado. Cada usuario arma su roadmap personal con los
cursos que quiere hacer y sigue su avance.

## 2. Objetivos y métricas de éxito

| Objetivo | Cómo sabemos que lo logramos |
|----------|------------------------------|
| Catálogo confiable y variado | ≥ 70% de los aportes terminan publicados; cursos publicados en al menos 6 de las 8 categorías |
| Validación ágil | El 80% de los aportes se valida dentro de las 48 h de enviados |
| El roadmap se usa para planificar | ≥ 50% de los usuarios registrados tiene al menos 3 cursos en su roadmap |
| Los cursos se completan | ≥ 20% de los ítems de roadmap llegan a estado `terminado` en 3 meses |

## 3. Personas

### Persona 1 — Laura, busca trabajo (rol `usuario`)

- **Contexto:** 27 años, vive en Berisso, terminó el secundario y busca empleo administrativo.
  Usa el celular para casi todo.
- **Motivaciones:** sumar cursos con certificado al CV lo antes posible y sin gastar plata.
- **Frustraciones hoy:** encuentra cursos sueltos en grupos de Facebook y no sabe si son
  gratuitos, si dan certificado o si siguen vigentes.
- **Historias tipo:**
  - "Quiero ver qué cursos de administración con certificado hay, sin registrarme."
  - "Quiero anotar los cursos que me interesan y en qué orden hacerlos."
  - "Hice un curso bueno de Excel y quiero que otros lo encuentren."

### Persona 2 — Martín, moderador (rol `moderador`)

- **Contexto:** 35 años, voluntario de un centro de formación barrial; revisa los aportes un par
  de veces por semana desde la computadora.
- **Motivaciones:** que el catálogo tenga solo cursos reales, gratuitos y con enlace activo.
- **Frustraciones hoy:** recibe pedidos de difusión por WhatsApp sin datos completos.
- **Historias tipo:**
  - "Quiero ver los aportes pendientes con todos sus datos para revisarlos rápido."
  - "Si rechazo un curso, quiero explicar por qué para que el aportante lo corrija."

## 4. User stories

| ID | Prioridad | Historia | Criterios de aceptación |
|----|-----------|----------|-------------------------|
| US-001 | P0 | Como **visitante**, quiero ver el catálogo comunitario paginado, buscar y filtrar, para descubrir cursos sin registrarme | - [ ] Dado que visito `/catalogo`, veo la primera página con hasta 12 cursos publicados<br>- [ ] Dado que hay más de 12 cursos, aparece el paginador y puedo ir a otra página<br>- [ ] Dado que escribo un texto, solo veo cursos cuyo título o tema lo contienen<br>- [ ] Dado que elijo categoría o modalidad, el listado se filtra y vuelve a la página 1<br>- [ ] Dado que no hay resultados, veo un empty state con CTA "Aportar un curso"<br>- [ ] Dado que toco un curso, veo su detalle completo (`/catalogo/:id`)<br>- [ ] Nunca veo cursos `en_revision` o `rechazado` |
| US-002 | P0 | Como **visitante**, quiero registrarme e iniciar sesión, para aportar cursos y tener mi roadmap | - [ ] Dado un email nuevo y contraseña de ≥ 8 caracteres, me registro y quedo logueado<br>- [ ] Dado un email ya registrado, veo "Ese email ya tiene una cuenta"<br>- [ ] Dado credenciales correctas, inicio sesión y vuelvo a la ruta desde la que vine<br>- [ ] Dado credenciales incorrectas, veo un mensaje genérico (no revela si el email existe)<br>- [ ] Dado más de 5 intentos de login en 1 minuto desde la misma IP, recibo "Demasiados intentos, probá en un minuto"<br>- [ ] Dado que cierro sesión, las rutas protegidas me redirigen a `/login` |
| US-003 | P0 | Como **usuario**, quiero aportar un curso con sus datos, para sumarlo al catálogo de la comunidad | - [ ] El formulario pide: URL, categoría (lista), tema, título, versión, año, docentes, modalidad, cuestionarios, examen, certificado, aval, "¿lo tomaste?" y la confirmación de gratuidad<br>- [ ] Dado un campo obligatorio vacío o una URL inválida, veo el error junto al campo<br>- [ ] Dado una URL ya cargada, veo "Ese curso ya fue aportado"<br>- [ ] Dado un envío válido, veo un toast "Aporte enviado a revisión" y el curso queda `en_revision`<br>- [ ] El curso no aparece en el catálogo hasta ser aprobado |
| US-004 | P0 | Como **moderador**, quiero aprobar o rechazar aportes, para que el catálogo solo tenga cursos válidos | - [ ] Dado que soy moderador, veo `/validar` en el menú; un usuario común no lo ve y recibe 403 si entra<br>- [ ] Veo los aportes `en_revision` paginados, del más antiguo al más nuevo, con todos sus datos y quién lo aportó<br>- [ ] Dado que apruebo, el curso pasa a `publicado` y aparece en el catálogo<br>- [ ] Dado que rechazo, debo escribir un motivo; el curso pasa a `rechazado` |
| US-005 | P0 | Como **usuario**, quiero agregar cursos del catálogo a mi roadmap y marcar mi avance, para planificar mi formación | - [ ] Desde el detalle de un curso publicado, toco "Agregar a mi roadmap" y queda como `pendiente` al final de la lista<br>- [ ] Si ya está en mi roadmap, el botón dice "En tu roadmap" y está deshabilitado<br>- [ ] En `/roadmap` veo mis cursos paginados y en orden<br>- [ ] Puedo cambiar el estado a `en curso` o `terminado`<br>- [ ] Puedo quitar un curso (con confirmación)<br>- [ ] Nadie más puede ver ni modificar mi roadmap |
| US-006 | P0 | Como **usuario**, quiero recuperar mi contraseña, para volver a entrar si la olvido | - [ ] En `/recuperar` ingreso mi email y siempre veo "Si el email existe, te enviamos un enlace"<br>- [ ] Se genera un enlace `/restablecer?token=…` válido por 30 minutos y de un solo uso<br>- [ ] Con un token válido defino una contraseña nueva (≥ 8 caracteres) y puedo iniciar sesión con ella<br>- [ ] Con un token vencido o usado veo "El enlace no es válido o venció" |
| US-007 | P1 | Como **usuario**, quiero ver el estado de mis aportes, para saber si fueron publicados | - [ ] En `/mis-aportes` veo mis aportes paginados con un badge de estado<br>- [ ] Si fue rechazado, veo el motivo |
| US-008 | P1 | Como **usuario**, quiero reordenar los cursos de mi roadmap, para decidir qué hago primero | - [ ] Cada ítem tiene botones "subir" / "bajar"<br>- [ ] El orden se mantiene al recargar la página |

## 5. Requisitos funcionales

| ID | Requisito | Historia relacionada |
|----|-----------|----------------------|
| RF-001 | El sistema lista solo cursos `publicado`, paginados, con búsqueda por texto y filtros por categoría y modalidad | US-001 |
| RF-002 | El sistema muestra el detalle de un curso publicado | US-001 |
| RF-003 | El sistema permite registrar usuarios con nombre, email y contraseña | US-002 |
| RF-004 | El sistema autentica con email y contraseña, limita los intentos de login y permite cerrar sesión | US-002 |
| RF-005 | El sistema permite a un usuario autenticado aportar un curso con todos los campos de la SPEC más modalidad | US-003 |
| RF-006 | El sistema rechaza aportes con URL duplicada | US-003 |
| RF-007 | El sistema permite a un moderador listar aportes pendientes y aprobarlos o rechazarlos con motivo | US-004 |
| RF-008 | El sistema permite agregar, listar, cambiar de estado y quitar cursos del roadmap personal | US-005 |
| RF-009 | El sistema genera tokens de recuperación de un solo uso con vencimiento y permite restablecer la contraseña | US-006 |
| RF-010 | El sistema lista los aportes del usuario con su estado y motivo de rechazo | US-007 |
| RF-011 | El sistema permite cambiar el orden de los ítems del roadmap | US-008 |

## 6. Requisitos no funcionales

| ID | Requisito | Notas |
|----|-----------|-------|
| RNF-001 | Todos los listados están paginados (API y pantalla) | heredado de SPEC clase |
| RNF-002 | Mobile-first con mejora progresiva; usable desde 360px | heredado de SPEC clase |
| RNF-003 | Menú sándwich en mobile | heredado de SPEC clase |
| RNF-004 | Contraseñas cifradas con bcrypt (cost 12) | heredado de SPEC clase |
| RNF-005 | Login limitado a 5 intentos por minuto por IP | heredado de SPEC clase (rate limiting) |
| RNF-006 | El catálogo responde en < 1 s con 1.000 cursos publicados | decisión propia |
| RNF-007 | Contraste AA y navegación completa por teclado | decisión propia |
| RNF-008 | El proyecto levanta completo con `docker compose up` en una máquina limpia | consigna Instancia B |

## 7. Reglas de negocio

| ID | Regla | Fuente |
|----|-------|--------|
| BR-001 | Un curso aportado no aparece en el catálogo comunitario hasta que un moderador lo aprueba | SPEC clase |
| BR-002 | Quien aporta un curso declara si lo tomó o no (campo obligatorio) | SPEC clase |
| BR-003 | El roadmap es personal y privado: cada usuario ve y modifica solo el suyo | decisión propia |
| BR-004 | No puede haber dos cursos con la misma URL (sin importar su estado, salvo `rechazado`) | decisión propia |
| BR-005 | Solo se aceptan cursos gratuitos; el aportante lo confirma con un checkbox obligatorio | SPEC clase |
| BR-006 | Rechazar un aporte exige un motivo (texto de 10 a 500 caracteres) | decisión propia |
| BR-007 | Solo se pueden agregar al roadmap cursos `publicado`, y cada curso una sola vez por usuario | decisión propia |
| BR-008 | Solo un aporte `en_revision` puede aprobarse o rechazarse | decisión propia |
| BR-009 | El año de edición está entre 1990 y el año actual | decisión propia |

## 8. Fuera de alcance (out of scope)

- Cursos pagos, pasarela de pagos o inscripción a cursos desde la app.
- Opiniones, calificaciones, recomendaciones o reseñas de cursos y docentes.
- Subida de material de estudio o archivos.
- Certificados emitidos por la plataforma.
- Roadmaps públicos o compartidos.
- Edición o baja de cursos publicados desde la UI.
- Gestión de usuarios y asignación de roles desde la UI (el moderador se crea por seed).
- Login social (Google, etc.).
- App móvil nativa.
- Envío real de emails (en el MVP el enlace de recuperación sale por el log del backend).

## 9. Supuestos y decisiones sobre la SPEC de clase

- La validación la hace un **moderador** humano, no la comunidad por votos ([SPEC §8 #1](SPEC.md#8-vacíos-de-la-spec-de-clase-y-decisiones-tomadas)).
- "Incluir en mi catálogo" significa **agregar al roadmap personal** ([SPEC §8 #3](SPEC.md#8-vacíos-de-la-spec-de-clase-y-decisiones-tomadas)).
- Se agrega el campo **modalidad** por el foco local del producto ([SPEC §8 #5](SPEC.md#8-vacíos-de-la-spec-de-clase-y-decisiones-tomadas)).
- Un aporte rechazado no se reenvía: el usuario puede aportarlo de nuevo como un aporte nuevo
  (la URL de un rechazado no cuenta como duplicada, BR-004).

## 10. Preguntas abiertas

| Pregunta | Impacto | Estado |
|----------|---------|--------|
| ¿Los aportes expiran si nadie los valida? | medio | abierta — en el MVP no expiran |
| ¿Cómo se detectan cursos publicados cuyo enlace dejó de funcionar? | medio | abierta — versión futura (chequeo periódico de URLs) |
| ¿Cómo se suman moderadores nuevos? | bajo | decidida para el MVP: por seed/base de datos (PRD §8) |
| ¿Se envían emails reales de recuperación? | medio | decidida para el MVP: log del backend ([TRD TD-006](TRD.md#2-decisiones-técnicas-mínimo-3)) |

---

**Referencias:**
- Atlassian — Plantilla PRD: https://www.atlassian.com/software/confluence/templates/product-requirements
- Atlassian — Plantilla de requirements: https://www.atlassian.com/software/confluence/templates/requirements
