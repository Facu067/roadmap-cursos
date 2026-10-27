# A Cursar

> WebApp comunitaria de cursos gratuitos con salida laboral para La Plata y alrededores:
> catálogo validado por la comunidad + roadmaps personales de formación.
>
> **Estado:** 📚 Documentación (Instancia A) — desarrollo pendiente de aprobación docente.

**Stack:** React 19 + Vite + pnpm · FastAPI + uv · PostgreSQL 18.6 · Docker Compose (dev)

- **Autor:** Fernandes, Facundo Nahuel
- **Usuario de GitHub:** [facu067](https://github.com/facu067)
- **Legajo:** 32828
- **Materia:** Desarrollo de Software S34 — UTN FRLP, 2026

---

## Qué hace

Muchas personas de la región buscan cursos cortos y gratuitos para conseguir trabajo, pero la
información está dispersa y no se sabe cuáles dan certificado, piden examen o están avalados.
En A Cursar cualquier usuario aporta un curso gratuito de cualquier plataforma o institución y
declara si lo tomó; un moderador lo valida y, una vez aprobado, entra al catálogo comunitario
público. Desde ahí cada persona arma su roadmap personal y marca su avance.

## Documentación

| Documento | Responde la pregunta | Link |
|-----------|----------------------|------|
| SPEC | ¿Qué nos pidieron construir y qué decidimos nosotros? | [docs/SPEC.md](docs/SPEC.md) |
| PRD | ¿Para quién, qué problema resuelve, qué hace y qué NO hace? | [docs/PRD.md](docs/PRD.md) |
| TRD | ¿Cómo lo construimos técnicamente y por qué? | [docs/TRD.md](docs/TRD.md) |
| UX-UI | ¿Cómo se ve, se siente y se comporta? | [docs/UX-UI.md](docs/UX-UI.md) |
| APPFlow | ¿Qué pantallas hay y cómo se navega? | [docs/APPFlow.md](docs/APPFlow.md) |
| BackendSchema | ¿Qué datos persistimos y qué API expone? | [docs/BackendSchema.md](docs/BackendSchema.md) |
| Implementation Plan | ¿En qué orden construimos y qué significa "hecho"? | [docs/IMPLEMENTATION_PLAN.md](docs/IMPLEMENTATION_PLAN.md) |
| Git Workflow | ¿Cómo se versiona y se mergea? | [docs/GIT_WORKFLOW.md](docs/GIT_WORKFLOW.md) |

## Cómo revisar este repo

- Los `.md` se renderizan solos en GitHub, incluidos los diagramas Mermaid (`flowchart`,
  `erDiagram`, `stateDiagram`).
- La historia de Git muestra el flujo: `git log --graph --oneline --all`.
- El primer commit es la SPEC de clase tal cual; el refinamiento está en la rama `docs/spec-refinement`.
- Preview local: VS Code con `Ctrl+Shift+V`.

## Estado del trabajo

| Instancia | Contenido | Estado |
|-----------|-----------|--------|
| A — Documentación | repo + 7 documentos + flujo de Git | ✅ enviada a aprobación el 26/10/2026 |
| B — Desarrollo del MVP | ejecución del Implementation Plan | ⏳ pendiente de aprobación |
| C — Clase 27/10 | testing, Docker multi-stage, gestión del cambio | ⏳ |

## Cómo levantarlo

> Disponible desde la Instancia B. Requisitos: Docker y Docker Compose.
>
> ```bash
> git clone https://github.com/facu067/roadmap-cursos.git
> cd roadmap-cursos
> cp .env.example .env
> docker compose up
> # Frontend: http://localhost:5173 · API: http://localhost:8000/docs
> ```

## Versionado

- **Flujo de Git:** GitFlow simplificado — `main` (versiones estables) ← `develop` (integración) ← una rama por
  documento o tarea (`docs/…`, `chore/…`, `feature/…`, `fix/…`), mergeada con `--no-ff` para que se vea en la historia.
  Detalle en [docs/GIT_WORKFLOW.md](docs/GIT_WORKFLOW.md).
- **Convención de commits:** Conventional Commits en español, en presente, minúscula y sin punto final
  (`docs(prd): agrega historias de usuario`).

---

## Licencia / uso

Trabajo Práctico — Desarrollo de Software, UTN FR La Plata, 2026.
