# TRD — Technical Requirements Document — A Cursar

> **Qué es:** el CÓMO técnico: arquitectura, stack, estructura de carpetas y convenciones.
> El producto vive en el [PRD](PRD.md); el contrato de datos y API en el [BackendSchema](BackendSchema.md).
> **Cuándo está bien:** un developer que no estuvo en la charla puede levantar el proyecto y tomar
> las mismas decisiones; cada decisión importante tiene alternativas, la opción elegida y el porqué.

## 1. Stack y versiones exactas

| Capa | Elección | Versión exacta | Por qué |
|------|----------|----------------|---------|
| Frontend | React + Vite + pnpm | React 19 · Vite (fijada en `pnpm-lock.yaml`) · pnpm 10.34.5 · Node v20.20.2 | fijado por SPEC; Vite da dev server rápido con proxy a la API |
| Routing | React Router | 7.x | estándar de facto para SPA con rutas protegidas |
| Backend | FastAPI + uv | FastAPI 0.158.2 · uv 0.12.19 · Python 3.13 | fijado por SPEC; validación con Pydantic y OpenAPI automático en `/docs` |
| ORM / driver | SQLModel + psycopg 3 | última stable al crear el proyecto | mismos modelos para tablas y schemas (Pydantic); menos código duplicado |
| Seguridad | `bcrypt` · `PyJWT` · `slowapi` | última stable | bcrypt exigido por SPEC; JWT para sesión; slowapi para rate limiting |
| BD | PostgreSQL | 18.6 | fijado por SPEC |
| Infra (dev) | Docker + Compose | Docker 29.9.0 · Compose 5.6.0 | fijado por SPEC |

## 2. Decisiones técnicas (mínimo 3)

| # | Decisión | Alternativas consideradas | Elegida | Por qué |
|---|----------|---------------------------|---------|---------|
| TD-001 | Autenticación y sesión | JWT en `localStorage` / sesión server-side en tabla / JWT en cookie httpOnly | **JWT (HS256) en cookie httpOnly, `SameSite=Lax`, 5 min** | El token no es accesible desde JS (mitiga XSS); no hace falta tabla de sesiones; el front y la API comparten origen gracias al proxy de Vite, así que la cookie viaja sola. El rol (`usuario`/`moderador`) va en el token y se re-verifica contra la BD en endpoints de moderación |
| TD-002 | Paginación | offset + total / cursor-based | **Offset: `page` (≥1) y `page_size` (1–50, default 12 en catálogo y 10 en el resto); respuesta `{items, total, page, page_size}`** | Permite el paginador numerado estilo Google («1 2 3 …») que pide la UX; el volumen esperado (miles) no justifica cursores |
| TD-003 | Formato de errores de API | envelope propio / RFC 7807 problem+json / errores por defecto de FastAPI | **Envelope propio `{ "error": { "code", "message", "details" } }`** con handlers globales que también envuelven los 422 de Pydantic | Un único formato para el front; `code` estable para lógica, `message` en español para mostrar |
| TD-004 | Modelar "mi roadmap" | array de IDs en usuario / tabla intermedia usuario-curso con estado | **Tabla `roadmap_items` (usuario, curso, orden, estado_avance)** con `UNIQUE(usuario_id, curso_id)` | Permite estados, orden y paginación con SQL simple; la unicidad la garantiza la BD (BR-007) |
| TD-005 | Rate limiting del login | middleware propio / `slowapi` en memoria / Redis | **`slowapi` en memoria: 5 intentos/minuto por IP en `POST /auth/login`** | Cumple la SPEC sin otro servicio; Redis queda como mejora si se escala a varias réplicas |
| TD-006 | Recuperación de contraseña | SMTP real / servicio externo (SendGrid) / log en desarrollo | **Token aleatorio de 32 bytes, guardado como hash SHA-256, válido 30 min, un solo uso; en dev el enlace se escribe en el log del backend** | Ciclo completo verificable sin depender de un proveedor de correo; el envío queda aislado en un `Notificador` reemplazable |
| TD-007 | Esquema de BD y datos iniciales | Alembic / `create_all` + seed | **`SQLModel.metadata.create_all` al iniciar + script de seed idempotente** | Suficiente para el MVP; Alembic entra cuando haya cambios de esquema con datos reales (riesgo §6) |
| TD-008 | Estilos | Tailwind / MUI / CSS propio con variables | **CSS propio con custom properties (tokens de la UX-UI) y CSS Modules** | Control total del mobile-first sin media queries "de arreglo"; el estilo Google se define en tokens, no depende de una librería |

## 3. Arquitectura

### 3.1 Patrón y capas

Screaming Architecture + arquitectura en capas dentro de cada dominio:

```mermaid
flowchart LR
    HTTP[router.py — endpoints] --> SVC[service.py — reglas de negocio]
    SVC --> REPO[repository.py — acceso a datos]
    REPO --> DB[(PostgreSQL)]
    SVC --> MODELS[models.py / schemas.py]
```

- **Routers:** reciben HTTP, validan input con schemas, resuelven dependencias (usuario actual, rol) y delegan.
- **Services:** reglas de negocio (BR-xxx) y transiciones de estado; testeables sin HTTP.
- **Repositories:** única puerta a la BD; sin reglas de negocio.
- **Dominios:** `identidad` (usuarios, sesión, recuperación), `cursos` (catálogo público y categorías),
  `aportes` (alta y validación), `roadmap` (roadmap personal). `compartido` tiene lo transversal.

### 3.2 Estructura de carpetas (la que se va a crear en la Instancia B)

```
roadmap-cursos/
├── backend/
│   ├── pyproject.toml
│   ├── uv.lock
│   ├── Dockerfile
│   ├── app/
│   │   ├── main.py                 # crea la app, registra routers y handlers
│   │   ├── config.py               # settings desde variables de entorno
│   │   ├── seed.py                 # categorías, usuarios demo y cursos de ejemplo
│   │   ├── identidad/              # registro, login, logout, me, recuperación
│   │   │   ├── router.py
│   │   │   ├── service.py
│   │   │   ├── repository.py
│   │   │   ├── models.py           # Usuario, TokenRecuperacion
│   │   │   ├── schemas.py
│   │   │   └── seguridad.py        # bcrypt, JWT, dependencia usuario_actual / requiere_moderador
│   │   ├── cursos/                 # catálogo público + categorías
│   │   │   ├── router.py
│   │   │   ├── service.py
│   │   │   ├── repository.py
│   │   │   ├── models.py           # Curso, Categoria, enums EstadoCurso y Modalidad
│   │   │   └── schemas.py
│   │   ├── aportes/                # alta de aportes, mis aportes, validación
│   │   │   ├── router.py
│   │   │   ├── service.py
│   │   │   ├── repository.py
│   │   │   └── schemas.py
│   │   ├── roadmap/                # roadmap personal
│   │   │   ├── router.py
│   │   │   ├── service.py
│   │   │   ├── repository.py
│   │   │   ├── models.py           # RoadmapItem, enum EstadoAvance
│   │   │   └── schemas.py
│   │   └── compartido/
│   │       ├── db.py               # engine y sesión
│   │       ├── paginacion.py       # params y respuesta paginada genérica
│   │       ├── errores.py          # excepciones de dominio + handlers (TD-003)
│   │       └── notificador.py      # "envío" de emails (log en dev, TD-006)
│   └── tests/                      # se completa en la clase del 27/10
├── frontend/
│   ├── package.json
│   ├── pnpm-lock.yaml
│   ├── vite.config.js              # proxy /api → backend:8000
│   ├── Dockerfile
│   └── src/
│       ├── main.jsx
│       ├── app/                    # router, layout, Header + menú sándwich, RutaProtegida
│       ├── features/
│       │   ├── identidad/          # Login, Registro, Recuperar, Restablecer, contexto de sesión
│       │   ├── catalogo/           # Home, Catalogo, DetalleCurso
│       │   ├── aportes/            # AportarCurso, MisAportes
│       │   ├── validacion/         # PanelValidacion (moderador)
│       │   └── roadmap/            # MiRoadmap
│       │       └── (cada feature: pages/, components/, hooks/, api.js)
│       ├── shared/
│       │   ├── ui/                 # Button, Input, Card, Paginador, Toast, Modal, Skeleton, EmptyState
│       │   └── api/cliente.js      # fetch con credentials y manejo del envelope de error
│       └── styles/
│           ├── tokens.css          # variables de UX-UI §2.4
│           └── base.css
├── docker-compose.yml
├── .env.example
└── docs/
```

### 3.3 Servicios Docker (entorno de desarrollo)

| Servicio | Imagen/base | Puerto | Rol |
|----------|-------------|--------|-----|
| postgres | `postgres:18.6` | 5432 | base de datos, volumen `pgdata` |
| backend | `python:3.13-slim` + uv | 8000 | API FastAPI con `--reload`; espera a postgres y corre el seed |
| frontend | `node:20-alpine` + pnpm (corepack) | 5173 | Vite dev server con proxy `/api` → `backend:8000` |

```mermaid
flowchart LR
    BROWSER[Navegador :5173] --> FE[frontend Vite]
    FE -- "/api/*" --> BE[backend FastAPI :8000]
    BE --> PG[(postgres :5432)]
```

> En la clase del 27/10 estos Dockerfiles evolucionan a multi-stage. Por ahora, lo mínimo que levanta.

## 4. Convenciones

- **Commits:** Conventional Commits en español, en presente, minúscula y sin punto final — `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, `chore:`, `style:`;
  scope opcional por dominio (`feat(roadmap): agrega endpoint para sumar cursos`).
- **Flujo de Git:** GitFlow simplificado (`main` ← `develop` ← una rama por tarea, merge con `--no-ff`) — detalle en [`GIT_WORKFLOW.md`](GIT_WORKFLOW.md).
- **API:** prefijo `/api/v1`, JSON, nombres en español y `snake_case`, fechas ISO 8601 UTC.
- **Errores de API (TD-003):**

  ```json
  {
    "error": {
      "code": "CURSO_DUPLICADO",
      "message": "Ese curso ya fue aportado.",
      "details": null
    }
  }
  ```

  Para errores de validación (422), `details` es una lista `[{ "campo": "url", "mensaje": "URL inválida" }]`.

- **Variables de entorno** (`.env.example` en la raíz):

  | Variable | Ejemplo | Obligatoria | Descripción |
  |----------|---------|-------------|-------------|
  | `POSTGRES_USER` | `acursar` | sí | usuario de la BD |
  | `POSTGRES_PASSWORD` | `acursar` | sí | contraseña de la BD (solo dev) |
  | `POSTGRES_DB` | `acursar` | sí | nombre de la BD |
  | `DATABASE_URL` | `postgresql+psycopg://acursar:acursar@postgres:5432/acursar` | sí | conexión del backend |
  | `JWT_SECRET` | `cambiar-en-cada-entorno` | sí | firma de los JWT |
  | `JWT_EXPIRA_MIN` | `60` | no | duración de la sesión |
  | `LOGIN_RATE_LIMIT` | `5/minute` | no | límite de intentos de login (TD-005) |
  | `BCRYPT_ROUNDS` | `12` | no | cost de bcrypt |
  | `FRONTEND_URL` | `http://localhost:5173` | sí | base del enlace de recuperación |
  | `SEED` | `true` | no | carga datos de ejemplo al iniciar |

- **Logging y excepciones:** logging estándar de Python a stdout (lo recoge Docker). Las reglas de
  negocio lanzan excepciones de dominio (`NoEncontrado`, `Conflicto`, `SinPermiso`, `ReglaInvalida`)
  que un handler global traduce a 404/409/403/422 con el envelope. Errores no esperados → 500
  genérico al cliente y traza completa en el log. Nunca se loggean contraseñas ni tokens.

## 5. Requisitos no funcionales técnicos (traducción de las restricciones)

| Restricción (SPEC) | Cómo se cumple | Dónde se verifica |
|--------------------|----------------|-------------------|
| Paginación de listados | TD-002, helper `compartido/paginacion.py` + componente `Paginador` | `GET /api/v1/cursos`, `/aportes/mios`, `/aportes/pendientes`, `/roadmap` |
| Mobile-first con mejora progresiva | `base.css` sin media queries; breakpoints 768/1024 solo agregan ([UX-UI §3](UX-UI.md#3-estrategia-mobile-first-con-mejora-progresiva)) | Catálogo, formulario de aporte y roadmap en 360px |
| Menú sándwich | `app/Header` con drawer y ARIA | [APPFlow §2](APPFlow.md#2-rutas) — todas las pantallas |
| Screaming Architecture | árbol §3.2 | revisión del repo |
| Contraseñas con bcrypt | `identidad/seguridad.py`, cost 12 | columna `password_hash` empieza con `$2b$12$` |
| Rate limiting en login | TD-005 | 6° intento en un minuto → 429 `DEMASIADOS_INTENTOS` |

## 6. Riesgos técnicos

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|------------|
| Cookie de sesión que no viaja por diferencias de origen | media | alto | Proxy de Vite (mismo origen); probarlo en M1 antes de seguir |
| `create_all` no migra cambios de esquema | alta | medio | En dev se recrea el volumen; adoptar Alembic al primer cambio con datos reales |
| Rate limiting en memoria se reinicia con el contenedor | baja | bajo | Aceptado en MVP; Redis si se escala |
| Formulario de aporte con 14 campos lleva más tiempo de lo estimado | media | medio | Un único schema Pydantic como fuente de validación + mensajes por campo genéricos en el front |
| Plazo corto de la Instancia B | alta | alto | Ejecutar solo P0 en orden (IMPLEMENTATION_PLAN §1); P1 si sobra tiempo |

---

**Referencia:**
- Design Docs at Google: https://www.industrialempathy.com/posts/design-docs-at-google/
