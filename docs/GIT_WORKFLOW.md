# Flujo de Git — A Cursar

## 1. Estrategia de ramas: GitFlow simplificado

```
main          ← solo versiones estables (lo que se entrega). No se commitea directo.
  └── develop ← integración: acá se mergean las ramas de trabajo. No se commitea directo.
        └── docs/refinar-spec                ← una rama por documento o tarea
        └── feature/US-002-identidad
        └── fix/validacion-url-duplicada
```

**Por qué este flujo:** separa lo estable (`main`, lo que se entrega y aprueba) de lo que se está
integrando (`develop`). Cada documento o tarea tiene su propia rama, así la historia muestra qué
entró, cuándo y por qué.

**Única excepción:** el primer commit (la SPEC de clase original, sin modificar) va directo a `main`
porque crea el repositorio, tal como lo pide la consigna.

## 2. Nomenclatura de ramas

```
feature/US-[NNN]-[descripcion-corta]   ← funcionalidad de una historia del PRD
fix/[descripcion-del-bug]
docs/[descripcion]
chore/[descripcion]
refactor/[descripcion]
test/[descripcion]
```

| Válidas | Inválidas |
|---------|-----------|
| `docs/refinar-spec` | `mi-rama` (sin contexto) |
| `feature/US-005-roadmap` | `feature/arreglos` (sin ID de historia) |
| `fix/obs-appflow-rutas` | `wip` (nunca se mergea trabajo en progreso) |

Las observaciones del docente se corrigen en una rama `fix/obs-[descripcion]` y en el hilo del correo
se informa el SHA del merge.

## 3. Convención de commits (Conventional Commits)

Formato: `tipo(alcance): descripción en presente, sin mayúscula inicial y sin punto final`.

| Tipo | Cuándo | Ejemplo |
|------|--------|---------|
| `feat` | nueva funcionalidad | `feat(roadmap): agrega endpoint para sumar cursos al roadmap` |
| `fix` | corrección | `fix(aportes): rechaza urls de cursos duplicadas` |
| `docs` | documentación | `docs(prd): agrega personas e historias de usuario` |
| `test` | tests | `test(aportes): cubre las transiciones de estado de validación` |
| `refactor` | cambio interno sin cambio de comportamiento | `refactor(cursos): extrae helper de paginación` |
| `chore` | mantenimiento (config, dependencias, docker) | `chore(docker): agrega servicio postgres 18.6` |
| `style` | formato sin cambio de lógica | `style(frontend): aplica formato de prettier` |

Si el commit implementa un requisito del PRD, se referencia en el cuerpo:

```
feat(aportes): agrega validación de url duplicada

Implementa RF-006 y BR-004.
```

Los merges también usan un mensaje convencional: `docs(prd): integra rama docs/prd`.

## 4. Cómo se mergea

- Las ramas de trabajo se mergean a `develop` con **merge commit (`--no-ff`)**, para que cada rama
  quede visible en la historia (`git log --graph`). Después del merge, la rama se borra.
- Cuando `develop` está lista para entregar, se mergea a `main` también con `--no-ff`:
  `chore(release): integra develop en main para la entrega de la instancia A`.
- **Revisión:** el trabajo es individual; antes de cada merge reviso el documento con la
  `CHECKLIST_ENTREGA.md`. El docente revisa el repo completo en la entrega.
- **Desde la Instancia B** (código), cada rama `feature/…` entra a `develop` por Pull Request en
  GitHub, para dejar registrada la revisión de cada historia.

## 5. Workflow paso a paso

```bash
# 1. Partir de develop
git switch develop

# 2. Crear la rama de la tarea
git switch -c docs/prd

# 3. Commitear (chico y específico)
git add docs/PRD.md
git commit -m "docs(prd): agrega personas e historias de usuario"

# 4. Volver a develop, mergear con --no-ff y borrar la rama
git switch develop
git merge --no-ff docs/prd -m "docs(prd): integra rama docs/prd"
git branch -d docs/prd

# 5. Entrega: develop → main y push de ambas
git switch main
git merge --no-ff develop -m "chore(release): integra develop en main para la entrega de la instancia A"
git push origin main develop
```

## 6. Variables de entorno

`.env` nunca se commitea (está en `.gitignore`). Cada variable nueva se agrega a `.env.example`
con un valor de ejemplo, se documenta en el TRD §4 y se menciona en el commit.
