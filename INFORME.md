# Informe — Discovery Agent

**Asignatura:** Ingeniería de Software — Unidad 1
**Maestría en Software, Universidad Politécnica Salesiana (UPS)**
**Caso de descubrimiento:** `cafestock` (cafetería pequeña: control de ventas e inventario)

---

## 1. Resumen

Este informe documenta la construcción y el uso de un **Discovery Agent** sobre
Claude Code: un agente genérico que convierte **evidencia de negocio cruda**
(entrevistas) en **artefactos de producto trazables** (personas, requisitos, user
stories, MVP Canvas) y en **hipótesis falsables con experimentos** para validarlos.

El agente se aplicó a un caso real, `cafestock`, y se ejecutó el flujo completo:
`analyze → generate-mvp → experiments → report`. El valor diferencial no es solo
generar artefactos, sino que **dos hooks (gates) protegen la calidad del proceso**:
impiden generar un MVP sobre evidencia insuficiente y rechazan hipótesis no
comprobables. Durante la sesión ambos gates **bloquearon de verdad**, y este
informe muestra cómo se resolvieron sin esquivarlos.

---

## 2. Evidencia recopilada y organizada (caso real)

El caso `cafestock` se sustenta en **3 entrevistas de primera mano**, todas en
español, anonimizadas y con frontmatter (`rol_entrevistado`, `primera_persona:
true`, `anonimizada: true`), en `discoveries/cafestock/interviews/`:

| Entrevista | Rol | Primera persona |
|---|---|---|
| `entrevista_01_dueno_cafeteria.md` | `dueno_negocio` | sí |
| `entrevista_02_empleado_ventas.md` | `empleado_ventas` | sí |
| `entrevista_03_encargado_compras.md` | `encargado_compras` | sí |

Se cumple y se supera el requisito de **al menos dos entrevistas de primera mano**
que respalden a las personas principales: cada una de las tres personas centrales
del caso tiene su propia entrevista. La evidencia es la **única fuente de verdad**
del discovery; los artefactos se derivan de ella, no al revés.

---

## 3. Configuración del proyecto Claude Code

El agente está montado con sus piezas fundamentales bajo `.claude/` y la raíz:

- **Constitución — `CLAUDE.md`.** Define las reglas no negociables del proyecto:
  cero invención, trazabilidad, personas de primera mano, aislamiento entre
  discoveries e idioma. Es lo que gobierna el comportamiento del agente.
- **Skill `discovery`.** Estándar de formato de todos los artefactos (personas,
  stakeholders, requisitos, user stories INVEST, MVP Canvas, test cards y los
  esquemas JSON de auditoría). Garantiza consistencia.
- **Comandos.** El flujo operativo:
  `/discovery:analyze`, `/discovery:generate-mvp`, `/discovery:experiments`,
  `/discovery:report`.
- **Hooks (gates).** `readiness-gate.py` y `hypothesis-gate.py`, hooks `PreToolUse`
  que validan la calidad antes de escribir ciertos artefactos.
- **Script de reporte.** `.claude/scripts/build-report.py`, generador determinista
  del dashboard HTML.

Estructura del caso:

```
discoveries/cafestock/
  interviews/   # 3 entrevistas (evidencia cruda)
  outputs/      # artefactos generados
```

---

## 4. Artefactos de descubrimiento generados (trazables)

Con `/discovery:analyze discoveries/cafestock` y
`/discovery:generate-mvp discoveries/cafestock` se generaron, **todos citando su
entrevista fuente**:

- **`personas.md`** — 3 personas (todas de primera mano) + 3 stakeholders (dueño;
  proveedores y clientes, mencionados pero no entrevistados) + un mapa de
  trazabilidad Mermaid entrevista → persona → dolor.
- **`requisitos.md`** — 11 requisitos candidatos: 8 funcionales (R-01…R-08) y 3 no
  funcionales (R-09…R-11), cada uno con origen.
- **`evidence-map.json`** — mapa de evidencia auditable: 3 personas y 10 dolores
  con su `source`.
- **`user-stories.md`** — 8 historias en formato **INVEST** (US-01…US-08) con
  criterios de aceptación (Dado/Cuando/Entonces) y fuente.
- **`mvp-canvas.md`** — MVP Canvas completo con métrica de éxito honesta y bloque
  explícito de "fuera de alcance por ahora".

**Núcleo de valor del MVP** (lo que más duele y más se repite entre las tres
personas): *registrar la venta rápido → descontar los insumos automáticamente →
alertar antes del quiebre de stock*. La **métrica de éxito** es el nº de quiebres
de stock de insumos clave por semana (pasa la prueba ácida: si baja, el dueño deja
de hacer compras de emergencia y de perder ventas).

Ningún dolor, persona o requisito se afirmó sin respaldo en una entrevista. Lo no
respaldado (proveedores, clientes) se marcó como mencionado, no como hecho.

---

## 5. El readiness-gate (calidad de la evidencia)

`readiness-gate.py` custodia la **puerta de salida del MVP**. Antes de escribir
`mvp-canvas.md` o `user-stories.md` valida, de forma independiente (no confía en lo
que el modelo reporte):

1. Existe `outputs/evidence-map.json`.
2. Hay al menos `MIN_INTERVIEWS` entrevistas (por defecto 2).
3. Cada **persona primaria** está respaldada por una entrevista **en primera
   persona** que exista en disco.
4. **Ningún dolor huérfano**: cada dolor cita una fuente que existe en disco.

Si algo falla → `exit 2` (bloquea) con el motivo. Es exactamente el control que
impide "generar el MVP cuando la evidencia es insuficiente (p. ej., una persona
principal sin entrevista propia)".

---

## 6. Hipótesis falsables y el hypothesis-gate

Con `/discovery:experiments discoveries/cafestock` se transformaron los supuestos
riesgosos del MVP Canvas en **4 hipótesis falsables**, ordenadas por riesgo, en
`hypotheses.md` (test cards legibles) y `experiment-board.json` (auditable):

| ID | Riesgo | Supuesto | Experimento más barato |
|---|---|---|---|
| **H-01** | alto | El empleado registra la venta en el momento en hora pico | Mago de Oz (1 sem) |
| **H-02** | alto | El descuento por receta estima bien el consumo real | Concierge (5 días) |
| **H-03** | medio | La alerta de stock bajo se traduce en reposición a tiempo | Mago de Oz (2 sem) |
| **H-04** | medio | Los mínimos "por experiencia" generan alertas útiles, no ruido | Concierge (2 sem) |

Cada hipótesis tiene señal medible **de negocio** (no de vanidad), criterio de
éxito **con número** (p. ej. ≥80% en 5 días) y **regla de decisión que contempla
el fallo** (perseverar / pivotar / descartar).

`hypothesis-gate.py` exige justo eso antes de escribir los artefactos: campos
completos, `threshold` con número/porcentaje, `decision` que diga qué hacer **si
falla**, y `metric` que **no sea de vanidad** (descargas, líneas de código, story
points, likes…). Una hipótesis que no se puede refutar no es hipótesis, es un deseo.

---

## 7. Demostración de los gates: bloqueo real y su resolución

Durante esta sesión **ambos gates bloquearon de verdad**. No se esquivaron (no se
escribió el artefacto con otro nombre ni en otra carpeta, ni se desactivó el hook a
ciegas): se diagnosticó la causa y se resolvió. Esto evidencia que el proceso
protege la calidad.

### 7.1 Bloqueo del readiness-gate — evidencia fuera de sitio

Al ejecutar `/discovery:generate-mvp`, el gate **bloqueó la escritura del MVP**:

```
✗ HOOK readiness-gate: evidencia insuficiente para generar el MVP
  • Solo hay 0 entrevista(s) en discoveries/cafestock/interviews; se requieren al menos 2.
  • Persona «Dueño de cafetería» (rol: dueno_negocio) no tiene una entrevista en
    primera persona en disco. Está construida de oídas.
  • … (igual para las otras dos personas)
  • Dolor «quiebres-stock» cita «entrevista_01_dueno_cafeteria.md», que no existe en interviews/.
  • …
```

**Diagnóstico (no se confió en el gate ni se lo esquivó):** las 3 entrevistas se
habían movido a `discoveries/_template/interviews/` (que debe estar vacío),
dejando `cafestock/interviews/` sin evidencia. El gate **tenía razón**: en disco
había 0 entrevistas. No se recrearon las entrevistas de memoria, porque eso violaría
la regla de cero invención.

**Resolución:** se devolvieron las 3 entrevistas a `cafestock/interviews/`. Al
reintentar, el gate pasó y el MVP se generó. *La calidad quedó protegida: sin
evidencia real en su sitio, no hay MVP.*

### 7.2 Bloqueo del hypothesis-gate — deadlock del hook

Al escribir `experiment-board.json`, el gate bloqueó con *"Falta
experiment-board.json"*. Aquí el bloqueo **no era por una hipótesis mala**, sino
por un **defecto del propio hook**: validaba el board leyéndolo **del disco**, pero
al ser un hook `PreToolUse` corre **antes** de escribir el archivo; como el board
estaba en la lista de archivos gateados, exigía que existiera para poder crearlo
(un *deadlock* de arranque).

**Verificación antes de tocar nada:** se colocó el board en una carpeta de pruebas
y se ejecutó el hook real contra él → `exit 0`. Esto confirmó que **el contenido de
las hipótesis era válido** y que el único problema era el deadlock.

**Resolución (con confirmación del usuario):** se corrigió el hook para que, cuando
el archivo a escribir sea el board, valide el **contenido del payload** en vez del
disco. Se conservan **todas** las reglas (señal medible, umbral con número, regla
de fallo, anti-vanidad); solo se eliminó la imposibilidad de arranque. Al
reintentar, el board y `hypotheses.md` pasaron el gate.

> **Lección:** un gate puede bloquear por dos motivos —evidencia/hipótesis
> deficiente (7.1) o un defecto en el propio control (7.2)—. En ambos casos la
> respuesta correcta es **diagnosticar y corregir la causa real**, nunca burlar el
> control.

---

## 8. Comunicación del descubrimiento (reporte visual)

Con `/discovery:report discoveries/cafestock` se generó
`discoveries/cafestock/outputs/report.html`: un **dashboard a color, autocontenido**
(un solo `.html`, sin dependencias) producido de forma **determinista** por
`build-report.py` a partir de los JSON. Contiene:

- **Personas con su respaldo** — verde = primera mano, ámbar = referenciada. En
  `cafestock`, las 3 personas en verde (no hay personas solo referenciadas).
- **Cadena de valor** output → outcome → impact del MVP.
- **Tablero de experimentos por riesgo** — alto = rojo (H-01, H-02), medio = ámbar
  (H-03, H-04), bajo = verde.

Sirve para presentar el discovery o imprimirlo.

---

## 9. Conclusiones

- Se recopiló y organizó evidencia de un **caso real** con **3 entrevistas de
  primera mano** que respaldan a las personas principales.
- El proyecto Claude Code quedó configurado con sus piezas fundamentales:
  **constitución, skill, comandos y hooks**.
- Se generaron artefactos **trazables y sin invención**: personas, requisitos,
  user stories INVEST y MVP Canvas.
- El **readiness-gate** impide generar el MVP sobre evidencia insuficiente; el
  **hypothesis-gate** rechaza hipótesis no comprobables o de vanidad.
- Se **demostró el funcionamiento de los gates** con bloqueos reales y su
  resolución, evidenciando que el proceso protege la calidad.
- El descubrimiento se **comunicó** mediante el reporte visual del agente.

El aprendizaje central de la Unidad 1 queda materializado: **entender el problema
antes de construir**, con evidencia trazable y controles que impiden avanzar sobre
supuestos sin validar.

---

## 10. Reflexión

### 10.1 ¿Qué supuesto mío sobre el problema cambió al revisar la evidencia real?

Entré al caso asumiendo que el dolor central de la cafetería era **el registro y el
cobro de las ventas** —un problema de "punto de venta": cuadrar la caja, no perder
plata al cobrar—. Al leer las tres entrevistas, ese supuesto se cayó. El dolor que
**se repite en las tres personas** y el que más cuesta dinero no es el cobro, sino
los **quiebres de stock**: quedarse sin leche un sábado al mediodía, salir a comprar
de emergencia (más caro), o sobrecomprar perecibles que se dañan. El registro de la
venta importa, pero como **medio** para descontar insumos y anticipar el quiebre, no
como fin. Eso cambió por completo el núcleo del MVP: la propuesta de valor pasó de
"controlar la caja" a "no quedarse sin insumos clave por sorpresa", y la métrica de
éxito pasó a ser el **nº de quiebres por semana**. La evidencia movió el centro de
gravedad del problema.

### 10.2 ¿Qué fue lo más difícil de cumplir la regla de "cero invención"?

Lo más difícil fue resistir la tentación de **rellenar los huecos con lo que "tiene
sentido"**. Dos momentos lo dejaron claro. Primero, cuando el readiness-gate bloqueó
el MVP porque las entrevistas no estaban en disco: la salida fácil era **reconstruir
las entrevistas de memoria** (las había leído minutos antes) y seguir; hacerlo
habría sido fabricar evidencia. La regla obligó a detenerse, diagnosticar y
**restaurar la fuente real** antes de continuar. Segundo, al describir a los
*proveedores* y *clientes*: es natural redactarlos como personas con dolores
propios, pero solo aparecían **mencionados** por otros, sin entrevista de primera
mano. Cero invención significó marcarlos explícitamente como "mencionados, no
entrevistados" y **no ascenderlos a hechos**. En resumen, lo difícil no es inventar
datos burdos, sino **no completar lo verosímil** que la evidencia no respalda.

### 10.3 ¿Para qué me serviría la idea de "gobernanza ejecutable" en mi trabajo?

La "gobernanza ejecutable" —reglas de calidad escritas como **código que bloquea**,
no como recomendaciones en un documento— es directamente aplicable a mi trabajo. Una
"definición de terminado", un estándar de trazabilidad o una política de "no se
mergea sin pruebas" suelen vivir en una wiki que **nadie aplica bajo presión de
entrega**. Convertirlas en *hooks* / *checks* automáticos (pre-commit, CI, gates de
PR) las hace **imposibles de saltar por descuido**: el control actúa solo, en el
momento exacto, y explica qué falta. En este proyecto lo vi en dos planos: el gate
**protegió la calidad** (no dejó generar un MVP sin evidencia real) y, cuando el
propio gate tenía un defecto, **obligó a arreglar la causa** en vez de degradar el
estándar. Esa es la lección que me llevo: la calidad sostenida no depende de la
disciplina individual, sino de **controles ejecutables** que la vuelven el camino
por defecto.
