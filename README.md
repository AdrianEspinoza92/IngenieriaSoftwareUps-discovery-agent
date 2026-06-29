# Discovery Agent

Agente de **Claude Code** que convierte **conocimiento de negocio crudo**
(entrevistas) en **artefactos de producto** —personas, stakeholders, requisitos,
user stories y MVP Canvas— y en **hipótesis con experimentos** para validarlos.

Es el producto de la **Unidad 1 de Ingeniería de Software** (Maestría en Software,
UPS). El objetivo no es escribir el código de la aplicación, sino **entender el
problema antes de construir**.

El agente es **genérico**: no está atado a ningún caso. Cada caso de negocio es un
**discovery** independiente bajo `discoveries/`.

## Estructura

```
discoveries/
  <nombre>/
    interviews/   # evidencia cruda: entrevistas en Markdown (única fuente de verdad)
    outputs/      # todo artefacto generado de ese discovery
  citasalud/      # discovery de ejemplo, completo (referencia / plan B de demo)
  cafestock/      # discovery de una cafetería (control de ventas e inventario)
  _template/      # discovery en blanco para copiar al empezar uno nuevo
.claude/          # el agente: skill `discovery`, comandos, hooks y scripts
CLAUDE.md         # reglas de trabajo del proyecto
```

Cada entrevista lleva frontmatter (`rol_entrevistado`, `primera_persona`,
`anonimizada`) y está en español.

## Flujo de trabajo

Cada comando recibe la carpeta del discovery como argumento:

1. **`/discovery:analyze <discovery>`** — lee la evidencia y produce
   `personas.md`, `requisitos.md` y `evidence-map.json`.
2. **`/discovery:generate-mvp <discovery>`** — genera `user-stories.md` (INVEST) y
   `mvp-canvas.md`.
3. **`/discovery:experiments <discovery>`** — convierte los supuestos riesgosos del
   MVP en hipótesis falsables y genera `experiment-board.json` y `hypotheses.md`.
4. **`/discovery:report <discovery>`** — genera `report.html`, un dashboard a color
   autocontenido (personas, cadena de valor y tablero de experimentos por riesgo).

Ejemplo:

```bash
/discovery:analyze discoveries/cafestock
/discovery:generate-mvp discoveries/cafestock
/discovery:experiments discoveries/cafestock
/discovery:report discoveries/cafestock
```

## Reglas de trabajo (no negociables)

1. **Cero invención.** Nunca se afirma un dolor, persona o requisito que no esté
   respaldado por una entrevista real del discovery.
2. **Trazabilidad.** Cada persona, dolor y requisito cita su archivo de entrevista
   fuente (por nombre).
3. **Personas de primera mano.** Una persona solo está respaldada si existe una
   entrevista *en primera persona* de ese rol. Que la mencionen otros no basta.
4. **Aislamiento entre discoveries.** Se trabaja solo dentro del discovery
   indicado; nunca se mezcla evidencia o artefactos de uno con otro.
5. **Idioma.** Español, salvo términos técnicos (user story, MVP, stakeholder).

## Los gates (reglas duras que se aplican solas)

Dos hooks de Claude Code validan la calidad antes de escribir ciertos artefactos.
Se auto-ubican: deducen a qué discovery pertenece la escritura desde la ruta.

- **Gate de readiness** (`.claude/hooks/readiness-gate.py`) — antes de escribir
  `mvp-canvas.md` o `user-stories.md`, valida que la evidencia sea suficiente:
  existe el mapa de evidencia, hay un mínimo de entrevistas, cada persona primaria
  tiene respaldo de primera mano y no hay dolores huérfanos. Si no, **bloquea**.
- **Gate de hipótesis** (`.claude/hooks/hypothesis-gate.py`) — antes de escribir
  `hypotheses.md` o `experiment-board.json`, valida que cada hipótesis sea
  **comprobable**: señal medible, criterio de éxito con número, regla de decisión
  que contemple el fallo y métrica que no sea de vanidad. Si no, **bloquea**.

La respuesta correcta a un bloqueo es **conseguir mejor evidencia o reescribir la
hipótesis**, no esquivar el gate.

## Discovery de ejemplo: `cafestock`

Caso de una cafetería pequeña que controla ventas e inventario de forma manual.
A partir de 3 entrevistas (dueño, empleado de ventas, encargado de compras) el
agente produjo el flujo completo en `discoveries/cafestock/outputs/`: personas y
requisitos, MVP centrado en *registrar la venta → descontar insumos → alertar
antes del quiebre*, 4 hipótesis priorizadas por riesgo y el reporte HTML.
