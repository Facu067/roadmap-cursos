# UX-UI — Guía visual de estilos — A Cursar

> **Qué es:** la gramática visual de la interfaz: tokens, componentes, estados y accesibilidad.
> La navegación entre pantallas vive en el [APPFlow](APPFlow.md).
> **Inspiración fijada por la SPEC:** Google / Material — limpieza, espacio en blanco, azul primario,
> sombras sutiles, bordes redondeados.
> **Cuándo está bien:** dos personas que sigan esta guía construyen la misma pantalla y queda
> igual; todos los estados de cada componente están definidos; la estrategia mobile-first es explícita.
> **Usuario principal:** Fany (PRD §3), que usa el celular para casi todo → todo se diseña primero
> para 360px y para el pulgar.

## 1. Principios (3 a 5)

1. **Mobile primero:** el layout base se diseña para 360px y se mejora hacia arriba; nunca se "repara" con media queries.
2. **La información que decide va primero:** en cada card se ve de un vistazo si da certificado, si
   tiene examen, la modalidad y quién lo avala — es lo que Fany necesita para elegir.
3. **Un solo color de acción por pantalla:** el azul primario marca la acción principal
   ("Aportar", "Agregar a mi roadmap", "Aprobar").
4. **Estados vacíos, de carga y de error son parte del diseño:** toda lista tiene skeleton, empty state con CTA y error con reintento.
5. **Lenguaje cercano y en voseo:** "Aportá un curso", "Tu roadmap está vacío".

## 2. Design tokens

### 2.1 Paleta de colores

| Token | Valor | Uso | Contraste AA |
|-------|-------|-----|--------------|
| `--color-primary` | `#1a73e8` | botones primarios, links, foco, ítem activo | vs `#ffffff` 4.51:1 ✓ |
| `--color-primary-dark` | `#185abc` | hover/press del primario, texto sobre `primary-container` | vs `#e8f0fe` 5.68:1 ✓ |
| `--color-primary-container` | `#e8f0fe` | chips de categoría, ítem activo del menú, página actual del paginador | — |
| `--color-on-primary` | `#ffffff` | texto/iconos sobre primario | vs `#1a73e8` 4.51:1 ✓ |
| `--color-surface` | `#ffffff` | cards, drawer, header, modales | — |
| `--color-background` | `#f8f9fa` | fondo de página | — |
| `--color-text` | `#202124` | texto principal | vs `#ffffff` 16.1:1 ✓ |
| `--color-text-muted` | `#5f6368` | metadatos (docentes, año, plataforma) | vs `#ffffff` 6.05:1 ✓ |
| `--color-border` | `#dadce0` | bordes de inputs y divisores (decorativo, ver §6) | — |
| `--color-hover-grey` | `#f1f3f4` | hover de botones de texto y filas | — |
| `--color-success` | `#137333` | badge "Publicado", estado "Terminado", toast de éxito | vs `#ffffff` 5.95:1 · vs `#e6f4ea` 5.24:1 ✓ |
| `--color-success-container` | `#e6f4ea` | fondo de badges de éxito | — |
| `--color-warning` | `#8a4b00` | texto del badge "En revisión" / "En curso" | vs `#fef7e0` 6.35:1 ✓ |
| `--color-warning-container` | `#fef7e0` | fondo de badges de aviso | — |
| `--color-error` | `#c5221f` | errores de validación, badge "Rechazado", botón peligroso | vs `#ffffff` 5.8:1 · vs `#fce8e6` 4.92:1 ✓ |
| `--color-error-container` | `#fce8e6` | fondo de badges y banners de error | — |

> Los verdes, ámbar y rojos son los tonos **oscuros** de la paleta Material (`#137333`, `#8a4b00`, `#c5221f`)
> en lugar de `#188038` / `#f9ab00` / `#d93025`, porque estos últimos no llegan a 4.5:1 sobre los fondos
> claros de los badges. Ratios verificados en https://webaim.org/resources/contrastchecker/.

### 2.2 Tipografía

| Token | Familia | Tamaño / line-height | Uso |
|-------|---------|----------------------|-----|
| `--font-family-base` | `"Roboto", system-ui, sans-serif` | — | toda la interfaz |
| `--text-sm` | | 14px / 20px | metadatos, badges, paginador |
| `--text-base` | | 16px / 24px | párrafos y campos (16px evita el zoom automático en iOS) |
| `--text-lg` | | 18px / 24px · peso 500 | título de card |
| `--text-xl` | | 22px / 28px · peso 400 | título de pantalla |
| `--text-2xl` | | 28px / 36px · peso 400 | hero de la Home (solo ≥ 768px) |

> Roboto self-host (`@fontsource/roboto`, pesos 400 y 500) con `font-display: swap`.

### 2.3 Espaciado, radios, sombras, z-index

| Token | Valor | Uso |
|-------|-------|-----|
| `--space-1` … `--space-8` | 4, 8, 12, 16, 24, 32, 40, 48 px | ritmo de 4px en toda la interfaz |
| `--radius-input` | 8px | inputs, selects |
| `--radius-card` | 12px | cards de curso e ítems de roadmap |
| `--radius-button` | 20px (pill) | botones, estilo Material 3 |
| `--radius-chip` | 8px | chips y badges |
| `--radius-modal` | 28px | modales |
| `--shadow-1` | `0 1px 2px rgba(60,64,67,.1), 0 1px 3px 1px rgba(60,64,67,.15)` | cards en reposo, header al hacer scroll |
| `--shadow-2` | `0 1px 2px rgba(60,64,67,.1), 0 2px 6px 2px rgba(60,64,67,.15)` | hover de card, drawer, modal |
| `--z-header` | 100 | header sticky |
| `--z-drawer` | 200 | menú sándwich + overlay |
| `--z-modal` | 250 | modales |
| `--z-toast` | 300 | toasts |

### 2.4 Bloque de variables listo para copiar

```css
:root {
  --color-primary: #1a73e8;
  --color-primary-dark: #185abc;
  --color-primary-container: #e8f0fe;
  --color-on-primary: #ffffff;
  --color-surface: #ffffff;
  --color-background: #f8f9fa;
  --color-text: #202124;
  --color-text-muted: #5f6368;
  --color-border: #dadce0;
  --color-hover-grey: #f1f3f4;
  --color-success: #137333;
  --color-success-container: #e6f4ea;
  --color-warning: #8a4b00;
  --color-warning-container: #fef7e0;
  --color-error: #c5221f;
  --color-error-container: #fce8e6;

  --font-family-base: "Roboto", system-ui, sans-serif;
  --text-sm: 0.875rem;
  --text-base: 1rem;
  --text-lg: 1.125rem;
  --text-xl: 1.375rem;
  --text-2xl: 1.75rem;

  --space-1: 4px;  --space-2: 8px;  --space-3: 12px; --space-4: 16px;
  --space-5: 24px; --space-6: 32px; --space-7: 40px; --space-8: 48px;

  --radius-input: 8px;
  --radius-card: 12px;
  --radius-button: 20px;
  --radius-chip: 8px;
  --radius-modal: 28px;

  --shadow-1: 0 1px 2px rgba(60, 64, 67, .1), 0 1px 3px 1px rgba(60, 64, 67, .15);
  --shadow-2: 0 1px 2px rgba(60, 64, 67, .1), 0 2px 6px 2px rgba(60, 64, 67, .15);

  --z-header: 100;
  --z-drawer: 200;
  --z-modal: 250;
  --z-toast: 300;
}
```

### 2.5 Uso con Tailwind CSS (TRD TD-008)

Los estilos se escriben con **Tailwind CSS 4**. Los tokens anteriores se cargan en `@theme`
(`frontend/src/styles/index.css`), así Tailwind genera clases con los valores de esta guía y no se
usan colores ni medidas "sueltos":

```css
@import "tailwindcss";

@theme {
  --font-sans: "Roboto", system-ui, sans-serif;

  --color-primary: #1a73e8;
  --color-primary-dark: #185abc;
  --color-primary-container: #e8f0fe;
  --color-on-primary: #ffffff;
  --color-surface: #ffffff;
  --color-background: #f8f9fa;
  --color-text: #202124;
  --color-text-muted: #5f6368;
  --color-border: #dadce0;
  --color-hover-grey: #f1f3f4;
  --color-success: #137333;
  --color-success-container: #e6f4ea;
  --color-warning: #8a4b00;
  --color-warning-container: #fef7e0;
  --color-error: #c5221f;
  --color-error-container: #fce8e6;

  --radius-input: 8px;
  --radius-card: 12px;
  --radius-button: 20px;
  --radius-chip: 8px;
  --radius-modal: 28px;

  --shadow-1: 0 1px 2px rgba(60, 64, 67, .1), 0 1px 3px 1px rgba(60, 64, 67, .15);
  --shadow-2: 0 1px 2px rgba(60, 64, 67, .1), 0 2px 6px 2px rgba(60, 64, 67, .15);

  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
}
```

| Token de esta guía | Clase de Tailwind |
|--------------------|-------------------|
| `--color-primary` | `bg-primary`, `text-primary`, `border-primary`, `ring-primary` |
| `--color-text-muted` | `text-text-muted` |
| `--radius-card` / `--radius-button` | `rounded-card` / `rounded-button` |
| `--shadow-1` / `--shadow-2` | `shadow-1` / `shadow-2` |
| `--space-1` … `--space-8` (4–48px) | escala nativa de Tailwind: `p-1`=4px, `p-2`=8px, `p-3`=12px, `p-4`=16px, `p-6`=24px, `p-8`=32px, `p-10`=40px, `p-12`=48px |
| `--text-sm` … `--text-2xl` | `text-sm` (14px), `text-base` (16px), `text-lg` (18px), `text-xl` (20px)*, `text-2xl` (24px)* |

\* Los tamaños de título se ajustan con `text-[22px]` y `text-[28px]` para respetar §2.2.

Ejemplo — botón primario (§4.1):

```jsx
<button className="min-h-11 rounded-button bg-primary px-6 text-sm font-medium text-on-primary
                   hover:bg-primary-dark hover:shadow-1 focus-visible:outline-2 focus-visible:outline-offset-2
                   focus-visible:outline-primary disabled:opacity-40">
  Aportar curso
</button>
```

## 3. Estrategia mobile-first con mejora progresiva

> Regla de la SPEC: la base **no** usa media queries; `@media (min-width: …)` solo agrega o mejora.
> Con Tailwind: las clases **sin prefijo** son la capa base; `md:` y `lg:` equivalen a `@media (min-width: 768px)`
> y `(min-width: 1024px)`. Ejemplo del grid del catálogo: `grid grid-cols-1 gap-4 md:grid-cols-2 lg:grid-cols-3`.
> Nunca se usan los prefijos `max-*` (serían media queries para "arreglar" hacia abajo).

| Capa | Dónde aplica | Qué define |
|------|--------------|------------|
| **Base (sin MQ)** | todos los viewports | 1 columna; header con hamburguesa + logo + acceso a sesión; cards apiladas; filtros del catálogo en un panel desplegable "Filtros"; formulario de aporte en 1 columna; botones de ancho completo en formularios; targets ≥ 44px; padding de página `--space-4` |
| `@media (min-width: 768px)` | tablet | grid de cards de 2 columnas; filtros visibles en una fila sobre el listado; formulario de aporte en 2 columnas para campos cortos (versión, año, modalidad); panel de validación como tabla real; hero de la Home con `--text-2xl` |
| `@media (min-width: 1024px)` | desktop | grid de 3 columnas; navegación horizontal en el header y hamburguesa oculta; contenedor `max-width: 1140px` centrado, padding `--space-5`; detalle de curso en 2 columnas (datos + acciones) |

## 4. Componentes (anatomía + estados)

### 4.1 Botón (primario / secundario / peligro)

- **Primario (filled):** fondo `--color-primary`, texto `--color-on-primary`, `--radius-button`,
  alto mínimo 44px, padding `10px 24px`, Roboto 500 14px. Hover: `--color-primary-dark` + `--shadow-1`.
- **Secundario (outlined):** borde 1px `--color-border`, texto `--color-primary`; hover `--color-hover-grey`.
- **Texto:** sin borde ni fondo, texto `--color-primary` (ej. "Cancelar", "Subir"/"Bajar").
- **Peligro:** texto `--color-error`; siempre abre un modal de confirmación (ej. "Quitar del roadmap", "Rechazar").
- Estados de todos: `default` · `hover` · `focus-visible` (anillo `2px solid --color-primary`, offset 2px) ·
  `active` · `disabled` (opacidad .38, sin pointer) · `loading` (spinner 18px + disabled, el texto no cambia de ancho).

### 4.2 Campo de formulario (input / select)

- Outlined: borde 1px `--color-border`, `--radius-input`, alto 48px, label siempre visible arriba
  en `--text-sm` `--color-text-muted`; `*` para obligatorios.
- Estados: `default` · `focus` (borde 2px `--color-primary`, label azul) · `filled` ·
  `error` (borde y label `--color-error` + mensaje debajo con icono, enlazado con `aria-describedby`) ·
  `disabled` (fondo `--color-hover-grey`).
- Checkbox de 20px con área táctil de 44px; los booleanos del aporte (cuestionarios, examen,
  certificado, "lo tomé", "es gratuito") se agrupan en un `fieldset` con `legend`.

### 4.3 Card de curso

- Superficie `--color-surface`, `--radius-card`, `--shadow-1`, padding `--space-4`; toda la card es un link al detalle.
- Contenido, en orden: chip de categoría (`--color-primary-container` / texto `--color-primary-dark`) ·
  título (`--text-lg`, máximo 2 líneas con ellipsis) · tema y docentes (`--text-sm`, `--color-text-muted`) ·
  fila de badges con icono: **Certificado**, **Examen**, **Avalado por …**, modalidad.
- Estados: `default` · `hover` (`--shadow-2`) · `focus-visible` (anillo azul) · `en mi roadmap`
  (icono check `--color-primary` arriba a la derecha + `aria-label` "En tu roadmap").

### 4.4 Lista/tabla paginada

- Base: cards apiladas (catálogo, mis aportes, roadmap, pendientes).
- ≥ 768px: el panel de validación pasa a tabla real (fecha, título, categoría, aportante, acciones),
  filas con divisor `--color-border` y hover `--color-hover-grey`.
- **Paginador estilo Google:** «‹ Anterior · 1 2 3 … 9 · Siguiente ›» centrado; botones circulares
  de 40px; página actual con fondo `--color-primary-container` y `aria-current="page"`; en mobile
  solo «‹», "Página 2 de 9" y «›». Arriba del listado: "Mostrando 13–24 de 97 cursos".
- Al cambiar de página se hace scroll al inicio del listado y se mantiene la página en la URL (`?page=2`).
- Vacía → empty state (§4.8).

### 4.5 Menú sándwich

- Botón hamburguesa (Material Symbols `menu`, 48×48) a la izquierda del header, visible < 1024px.
- Panel desde la izquierda: ancho `min(280px, 85vw)`, `--color-surface`, `--shadow-2`, borde derecho
  redondeado `0 16px 16px 0`; overlay `rgba(32,33,36,.6)`.
- Ítems de 48px: Inicio · Catálogo · Aportar curso* · Mis aportes* · Mi roadmap* · Validar aportes** ·
  Ingresar / Cerrar sesión (*con sesión, **solo moderador). Activo: fondo `--color-primary-container`.
- Estados: `cerrado` · `abierto`. Cierre con link, botón X, `ESC` o click en el overlay.
- ARIA: `aria-expanded` y `aria-controls` en el botón; foco atrapado en el panel; al cerrar vuelve al botón.

### 4.6 Modal

- `--color-surface`, `--radius-modal`, `--shadow-2`, overlay igual al drawer, ancho `min(400px, 90vw)`.
- Título + texto + acciones a la derecha (Cancelar texto / acción primaria o de peligro).
- Uso: quitar del roadmap y rechazar aporte (con textarea de motivo, 10–500 caracteres, BR-006).
- `role="dialog"`, `aria-modal="true"`, foco atrapado, cierre con `ESC`.

### 4.7 Toast (notificación)

- Fondo `#323232`, texto blanco, `--radius-chip`, ancho `min(344px, 90vw)`.
- Posición: inferior centrado (mobile) / inferior izquierda (≥ 1024px). `role="status"` (éxito/info) y `role="alert"` (error).
- Duración 5 s; uno a la vez. Ej.: "Aporte enviado a revisión", "Agregado a tu roadmap", "Curso aprobado".

### 4.8 Skeleton y Empty state

- Skeleton: bloques `--color-hover-grey` con la forma de la card; 6 en el catálogo mientras carga.
- Empty states:
  - Catálogo sin resultados: "No encontramos cursos con esos filtros" + "Limpiar filtros" + "Aportar un curso".
  - Roadmap vacío: "Tu roadmap está vacío" + "Explorar el catálogo".
  - Mis aportes vacío: "Todavía no aportaste cursos" + "Aportar el primero".
  - Validación vacía: "No hay aportes pendientes 🎉".
- Error de carga: banner `--color-error-container` con mensaje y botón "Reintentar".

### 4.9 Badge de estado (agregado: estados de curso y de roadmap)

| Estado | Texto | Fondo | Color de texto |
|--------|-------|-------|----------------|
| `en_revision` | En revisión | `--color-warning-container` | `--color-warning` |
| `publicado` | Publicado | `--color-success-container` | `--color-success` |
| `rechazado` | Rechazado | `--color-error-container` | `--color-error` |
| `pendiente` (roadmap) | Pendiente | `--color-hover-grey` | `--color-text-muted` |
| `en_curso` (roadmap) | En curso | `--color-warning-container` | `--color-warning` |
| `terminado` (roadmap) | Terminado | `--color-success-container` | `--color-success` |

Siempre texto + color (nunca solo color), `--radius-chip`, `--text-sm` peso 500, padding `2px 8px`.

## 5. Layout y navegación

- **Header:** sticky (`--z-header`), alto 56px (mobile) / 64px (≥ 1024px), fondo `--color-surface`;
  izquierda: hamburguesa + logo "A Cursar"; derecha: "Ingresar" o inicial del usuario. Sombra
  `--shadow-1` solo al hacer scroll. ≥ 1024px: links de navegación en el centro.
- **Contenedor:** ancho completo con padding `--space-4`; ≥ 1024px `max-width: 1140px` centrado.
- **Pantallas:** Home (buscador grande + últimos cursos), Catálogo, Detalle, Aportar, Mis aportes,
  Mi roadmap, Validar, Login, Registro, Recuperar, Restablecer. Detalle en el [APPFlow §3](APPFlow.md#3-fichas-de-pantalla).

## 6. Accesibilidad mínima (no negociable)

- [x] Texto principal `#202124` (16.1:1) y secundario `#5f6368` (6.05:1) sobre blanco → AA
- [x] Botón primario: blanco sobre `#1a73e8` (4.51:1) → AA
- [x] Badges con texto oscuro sobre fondo claro (todos ≥ 4.5:1, ver §2.1)
- [ ] `#dadce0` es borde **decorativo**: el contraste 3:1 del control lo da el estado `focus` (`#1a73e8`)
- [ ] `:focus-visible` en todo elemento interactivo
- [ ] Menú sándwich con `aria-expanded` / `aria-controls` y foco gestionado
- [ ] `prefers-reduced-motion: reduce` desactiva shimmer y transiciones
- [ ] Iconos decorativos con `aria-hidden="true"`; botones solo-icono con `aria-label`
- [ ] Errores de formulario asociados al campo con `aria-describedby`; al enviar con errores el foco va al primer campo inválido
- [ ] Targets táctiles ≥ 44×44px

## 7. Iconografía y assets

- Set: **Material Symbols Outlined** (el set de Google), self-host de los iconos usados como SVG.
- Por qué: coherente con la inspiración Google y con los nombres de Material (`menu`, `school`,
  `workspace_premium` para certificado, `quiz` para examen, `verified` para aval, `location_on` para presencial).
- Tamaño: 20px en botones y badges, 24px en header y listas; color heredado del texto (`currentColor`).
- Sin imágenes de stock; empty states con un icono grande (48px) en `--color-text-muted`.

---

**Referencias:**
- NN/g — Front-End Style-Guides: https://www.nngroup.com/articles/front-end-style-guides/
- NN/g — Design Systems 101: https://www.nngroup.com/articles/design-systems-101/
- Material Design 3: https://m3.material.io/
