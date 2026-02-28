---
name: setup-management
description: Bootstrapea un Management Center completo en el workspace actual. Crea la estructura de directorios, CLAUDE.md con workflows, templates, skeleton files y skills de runtime. Usar una vez por máquina/proyecto nuevo.
argument-hint: [path-destino]
user-invocable: true
---

# Setup Management Center

Crea toda la arquitectura de un Management Center desde cero: estructura de carpetas, CLAUDE.md con 11 workflows, templates, skeleton files, y los skills de runtime (init-initiative, ingest-doc).

---

## Flujo obligatorio

Seguir estos pasos en orden estricto.

---

### Paso 1: Recopilar parámetros

Hacer estas 5 preguntas al usuario (una por una, en orden):

1. **Nombre del equipo** — ej: "Platform Engineering", "Growth Backend"
2. **Misión en una línea** — qué hace y qué es owner el equipo (ej: "Proveemos la infraestructura de pagos para el ecosistema")
3. **Jira project key** — ej: "PLATFORM", "GROW" (o "none" si no usan Jira)
4. **Jira domain** — ej: "mycompany.atlassian.net" (o "none" si no usan Jira)
5. **Quarter actual** — ej: "Q1-2026", "Q2-2026"

Si el usuario pasó un argumento al invocar el skill, usarlo como path destino. Si no, preguntar:

6. **Path destino** — directorio donde crear `management/` (default: `.` = directorio actual)

Guardar las respuestas como:
- `TEAM_NAME` = respuesta 1
- `TEAM_MISSION` = respuesta 2
- `JIRA_KEY` = respuesta 3 (vacío si "none")
- `JIRA_DOMAIN` = respuesta 4 (vacío si "none")
- `CURRENT_QUARTER` = respuesta 5
- `BASE_PATH` = respuesta 6 (default ".")

Mostrar un resumen de los parámetros y pedir confirmación antes de continuar.

---

### Paso 2: Crear estructura de directorios

Crear los siguientes directorios (usar Bash con mkdir -p):

```
{BASE_PATH}/management/
{BASE_PATH}/management/initiatives/
{BASE_PATH}/management/templates/
{BASE_PATH}/management/outputs/
{BASE_PATH}/management/.claude/
{BASE_PATH}/management/.claude/skills/
{BASE_PATH}/management/.claude/skills/init-initiative/
{BASE_PATH}/management/.claude/skills/ingest-doc/
```

---

### Paso 3: Escribir management/CLAUDE.md

Escribir el siguiente contenido en `{BASE_PATH}/management/CLAUDE.md`.
Reemplazar todos los placeholders: TEAM_NAME, TEAM_MISSION, JIRA_KEY, JIRA_DOMAIN, CURRENT_QUARTER.
Si JIRA_KEY o JIRA_DOMAIN son vacíos, reemplazar las referencias de Jira con "N/A" o indicaciones de "configurar si aplica".

=== INICIO: management/CLAUDE.md ===

# Management Center — TEAM_NAME

**Equipo**: TEAM_NAME
**Proyecto Jira**: JIRA_KEY
**Herramientas**: Jira, Google Drive, Slack

---

## 🧭 Identidad del equipo

TEAM_MISSION

**Scope guard**: Completar con los límites reales de ownership del equipo.

<!-- Completar: qué sí hace el equipo -->
<!-- Completar: qué NO hace el equipo (igual de importante que lo que sí hace) -->

---

## Configuracion

**Jira base URL**: https://JIRA_DOMAIN
**Jira project key**: JIRA_KEY

---

## Estructura

```
management/
├── CLAUDE.md              # Este archivo
├── team.md                # Composición, skills y carga del equipo
├── inbox.md               # Captura rápida de lo que llega
├── initiatives/
│   ├── _index.md          # Índice y estado de todas las iniciativas
│   └── {slug}/
│       ├── initiative.md  # Archivo central (fuente de verdad)
│       ├── resources.md   # Docs, Slack, reuniones, contactos
│       ├── dependencies.md # Dependencias con otros equipos
│       └── docs/          # Input externo: PRDs, RFCs, diseños
├── templates/
│   ├── initiative.md
│   ├── resources.md
│   └── dependencies.md
└── outputs/
    └── monthly-YYYY-MM.md # Updates ejecutivos mensuales
```

---

## Workflows

### 1. Captura rápida

Cuando el usuario vuelque información suelta sin contexto de iniciativa:
- Agregar a `inbox.md` con fecha
- Si es claramente parte de una iniciativa existente, preguntar si moverlo ahí

### 2. Nueva iniciativa

Cuando el usuario quiera crear una iniciativa:
1. Pedir nombre y contexto mínimo
2. Crear carpeta `initiatives/{slug}/` con subcarpetas `docs/` y `plan/`
3. Copiar estos templates:
   - `initiative.md` (archivo central)
   - `resources.md` (docs, Slack, reuniones, contactos)
   - `dependencies.md` (iniciativas y equipos bloqueantes)
4. Agregar entrada en `initiatives/_index.md`
5. Si hay Jira epic ya creado, buscar stories y completar `## Plan de ejecucion` de inmediato

### 3. Volcar información en una iniciativa

Cuando el usuario traiga información parcial (notas de reunión, Slack, etc.):
1. Identificar a qué iniciativa pertenece
2. Agregar en la sección "Notas crudas" con fecha
3. Si la información es estructurada (alcance, dependencias, etc.), sugerir moverla a la sección correspondiente

### 4. Revisión de iniciativa

Cuando el usuario pida revisar una iniciativa:
1. Leer `initiative.md` completo
2. Leer `resources.md` y `dependencies.md`
3. Leer TODOS los archivos en `docs/` (PRDs, RFCs, lo que haya)
4. Cruzar información entre documentos
5. Producir análisis crítico de:
   - **Completitud**: qué secciones están vacías o incompletas
   - **Ambigüedades**: qué se puede interpretar de más de una forma
   - **Dependencias**: qué depende de otros equipos y no está resuelto
   - **Inconsistencias**: contradicciones entre docs/ y lo documentado
   - **Riesgos**: qué puede salir mal
   - **Gaps**: qué información falta para poder planificar
6. Escribir hallazgos en la sección "Gaps detectados" del initiative.md

### 5. Planificación

Cuando el usuario pida planificar una iniciativa:
1. Verificar que no haya gaps críticos. Si los hay, advertir antes de continuar
2. **¿Jira activo?** Si la iniciativa ya tiene epic en Jira o puede crearse, ir directo al Workflow 8 y saltear la generación de archivos en `plan/`. Usar `plan/` solo como borrador previo cuando Jira no está disponible.
3. Generar epics en `plan/` como archivos individuales (solo si no se va directo a Jira):
   - Descripción clara del epic
   - Criterios de aceptación
   - Complejidad estimada (S / M / L / XL)
   - Dependencias con otros epics o equipos
4. Para cada epic, generar stories dentro del mismo archivo:
   - Formato: Como [rol] quiero [acción] para [beneficio]
   - Criterios de aceptación específicos
   - Perfil sugerido (backend / frontend)
   - Complejidad (story points o talla)
5. Actualizar el initiative.md sección "Plan de ejecución" con resumen

### 6. Update mensual

Cuando el usuario pida el update mensual:
1. Leer todas las iniciativas con estado "en ejecucion"
2. Compilar por cada una: avance, blockers, próximos pasos
3. Generar `outputs/monthly-YYYY-MM.md` en formato ejecutivo
4. El formato debe ser directo para copiar a slides:
   - Título de iniciativa
   - Estado (semáforo: verde/amarillo/rojo)
   - Logros del mes
   - Blockers o riesgos
   - Próximos pasos

### 7. Carga del equipo

Cuando el usuario pida ver la carga:
1. Leer `team.md`
2. Leer todas las iniciativas en ejecución y sus asignaciones
3. Cruzar y mostrar distribución de carga por dev
4. Alertar si alguien está sobreasignado o subasignado

### 8. Exportar iniciativa a Jira

Cuando el usuario pida exportar una iniciativa a Jira:
1. Leer `initiative.md` completo
2. Leer `plan/` (si existen epics/stories generados)
3. En Jira, usar MCP Atlassian para:
   - **Crear Epic**: Nombre = initiative.md título, descripción = contexto
   - **Mapear stories**: Si existen en plan/, crear stories vinculadas al epic
   - **Vincular docs**: Agregar links a resources.md en descripción del epic
4. Actualizar los archivos locales:
   - `initiative.md` sección "Plan de ejecución": Epic Jira ID y link
   - `resources.md` sección "Links útiles": Epic Jira ID y link

**Limitaciones**:
- Solo exportar cuando la iniciativa está en "en refinamiento" o "planificada"
- No sincroniza cambios automáticamente (es exportación manual)

### 9. Sincronizar estado desde Jira

Cuando el usuario pida sincronizar información desde Jira:
1. Especificar el Epic Jira a sincronizar
2. Usar MCP Atlassian para:
   - Consultar estado del epic
   - Listar stories del epic
   - Obtener comentarios/notas recientes
3. Actualizar initiative.md con:
   - Estado actual de stories (abierta/en progress/done)
   - Notas de progreso en "Notas crudas" con fecha
4. Alertar si hay discrepancias entre Markdown y Jira

**Nota**: Markdown es la fuente de verdad para decisiones, pero Jira es fuente de verdad para estado de trabajo en curso.

### 10. Revisión de carry-over al cierre de quarter

Cuando el usuario pida la revisión de cierre de quarter:
1. Leer todas las iniciativas en `_index.md` del quarter que cierra
2. Para cada una en estado "en ejecucion" o "en refinamiento":
   - ¿Hay stories abiertas que no terminan antes del cierre? → marcar `Carry Over: QX-YYYY`
   - ¿Termina en el quarter? → dejar `Carry Over: -`, pasar a "cerrada" cuando done
3. Actualizar `initiative.md` de cada iniciativa con el campo Carry Over
4. Actualizar `_index.md`: mover/agregar las iniciativas con carry-over a la sección del nuevo quarter
5. Crear la sección del nuevo quarter en `_index.md` si no existe

### 11. Crear iniciativa para quarter futuro

Cuando el usuario quiera planificar una iniciativa para un quarter futuro:
1. Crear la iniciativa normalmente (Workflow 2)
2. En el header de `initiative.md`, setear `Quarter: QX-YYYY`
3. Agregar la entrada en la sección correcta de `_index.md`
4. Estado inicial: `borrador` (no se refina hasta acercarse al quarter)

---

## Scope Guard

Cuando revises una iniciativa, marcar como **OUT OF SCOPE** si incluye algo que está fuera del ownership del equipo (ver sección "🧭 Identidad del equipo" arriba).

Si está fuera de scope, documentarlo y sugerir conversación con el stakeholder correspondiente.

---

## Risk Patterns

<!-- Completar con los risk patterns específicos del dominio del equipo. -->
<!-- Ejemplo de formato:
### Dominio A
- ⚠️ Descripción del riesgo recurrente
- ⚠️ Otro riesgo conocido

### Dominio B
- ⚠️ Riesgo específico
-->

---

## Estados y transiciones

| Estado | Descripción | Condición de entrada | Cuándo pasar al siguiente |
|---|---|---|---|
| **borrador** | Información inicial, todavía incompleta | Idea llega | Cuando se detiene el flujo de info nueva |
| **en refinamiento** | Se está completando activamente, gaps siendo cubiertos | Hay >30% del contenido | Gaps resueltos, info estable |
| **planificada** | Epics y stories listos, listo para arrancar | Gaps resueltos, no hay dependencias bloqueantes | Dev empieza a trabajar |
| **en ejecucion** | Devs trabajando, reportar avance semanal | Primer dev asignado comienza | Todas las stories done |
| **bloqueada** | Esperando a otro equipo o recurso externo | Dependency externa crítica sin resolución | Dependency resuelta |
| **cerrada** | Done o cancelada, documentar por qué | Todas las stories done u orden de cancel | Archivo definitivamente |

---

## Quarters y planificacion trimestral

### Definición

Los quarters siguen el calendario fiscal estándar:
- **Q1**: Enero–Marzo
- **Q2**: Abril–Junio
- **Q3**: Julio–Septiembre
- **Q4**: Octubre–Diciembre

Cada iniciativa tiene dos campos en su header:
- `**Quarter**: CURRENT_QUARTER` — quarter en que fue creada o planificada
- `**Carry Over**: -` — si continúa al siguiente quarter: `Q2-2026`, etc.

### Reglas de Quarter

- **No mezclar quarters en la misma sección de `_index.md`**. Cada sección H2 representa un quarter.
- **Carry-over no es falla**. Es normal que iniciativas complejas spanneen varios quarters. Documentarlo explícita y tempranamente.
- **La sección Q siguiente en `_index.md` puede estar vacía** hasta que se carguen iniciativas o se confirmen carry-overs.
- **Al cierre de quarter**: archivar iniciativas "cerradas" moviendo su entrada a una subsección `### Cerradas` dentro del quarter correspondiente.

---

## Preguntas críticas por tipo de iniciativa

### Si es una migración
- ¿Legacy y nuevo conviven o es cutover?
- ¿Cuál es el plan de rollback?
- ¿Qué sucede con datos en vuelo?
- ¿Timeline de apagado definitivo del legacy?

### Si es un apagado
- ¿Hay dependencias de otros equipos?
- ¿Cuál es el plan de comunicación?
- ¿Hay migración de elementos afectados?
- ¿Se guarda data para audit?

### Si es una iniciativa cross (multi-dominio)
- ¿Cuáles son los dominios incluidos y cuáles están explícitamente fuera?
- ¿Cada dominio tiene un owner claro?
- ¿Algún dominio tiene owner externo al equipo?
- ¿Los dominios tienen dependencias entre sí o son independientes?

### Si es configuración de campaña
- ¿Quién valida los límites/CAPs?
- ¿Cuándo se enciende/apaga? (fecha/hora)
- ¿Hay convivencia con configuraciones legacy?
- ¿Quién monitorea durante vigencia?

---

## Formato de outputs

### Epic en plan/

```markdown
# EPIC: [Nombre]

**Descripción**: [Qué se logra con este epic]

**Criterios de aceptación**:
- [ ] AC1
- [ ] AC2

**Complejidad**: S | M | L | XL
**Dependencias externas**: [Si aplica]
**Riesgos**: [Si aplica]

## Stories

[STORY-001, STORY-002, ...]
```

### Story en plan/

```markdown
# STORY-NNN: Como [rol] quiero [acción] para [beneficio]

**Criterios**:
- [ ] Criterio 1
- [ ] Criterio 2

**Perfil**: Backend | Frontend
**Puntos**: [S|M|L|XL]
**Parte de**: EPIC-XXX
**Bloqueada por**: [Si aplica]
```

---

## Comando: Diagnóstico rápido

Cuando el usuario pida "estado" o "diagnóstico":
1. Leer todas las iniciativas en `_index.md`
2. Para cada una en "en ejecucion", leer initiative.md resumido
3. Reportar:
   - Total de iniciativas por estado
   - Devs sobreasignados (>2 iniciativas concurrentes)
   - Dependencias bloqueantes activas
   - Gaps críticos abiertos
4. Destacar alertas rojo/amarillo

---

## Reglas

- **No inventar información.** Si algo no está documentado, marcarlo como gap. Nunca rellenar con suposiciones.
- **Ser crítico y directo.** Al revisar iniciativas no suavizar problemas. El objetivo es detectar issues temprano.
- **docs/ es read-only.** Los archivos en docs/ son input externo. Leerlos para análisis pero no modificarlos.
- **initiative.md es la fuente de verdad.** Todo lo relevante de docs/ debe terminar reflejado ahí.
- **Preguntar antes de asumir.** Si no queda claro a qué iniciativa pertenece algo, o qué acción tomar, preguntar.
- **Respetar el estado de la iniciativa.** No generar plan si está en borrador. No cerrar si hay gaps críticos.
- **Owners siempre son miembros del equipo.** Al asignar Propietario o Devs, leer `team.md` y usar el nombre exacto. Resolver diminutivos o apodos contra ese listado antes de escribir.

=== FIN: management/CLAUDE.md ===

---

### Paso 4: Escribir templates

**4a. Escribir `{BASE_PATH}/management/templates/initiative.md`:**

=== INICIO: templates/initiative.md ===

# [Nombre de la Iniciativa]

> **Estado**: borrador
> **Prioridad**: -
> **Propietario**: -
> **Jira Epic**: -
> **Recursos**: Ver [`resources.md`](./resources.md)
> **Dependencias**: Ver [`dependencies.md`](./dependencies.md)
> **Quarter**: CURRENT_QUARTER
> **Carry Over**: -

## Contexto

<!-- ¿Por qué existe esta iniciativa? ¿De dónde viene? ¿Qué problema resuelve? -->

**Timeline clave**:
<!-- Agregar fechas a medida que se confirmen. Formato obligatorio para el dashboard:   -->
<!-- - **DD/MM/YYYY**: Descripción del hito                                             -->

## Notas crudas

<!-- Volcá acá lo que va llegando, con fecha. Sin formato, sin presión. -->
<!-- - YYYY-MM-DD: ... -->

## Alcance

### Dentro de scope

<!-- ¿Qué entra en esta iniciativa? -->

### Fuera de scope

<!-- ¿Qué queda explícitamente afuera? Esto es tan importante como lo que incluye. -->

## Equipo asignado

| Dev | Perfil | Responsabilidad |
|---|---|---|

## Plan de ejecucion

<!-- Completar cuando exista epic en Jira. Si ya hay epic, buscar stories y traerlas. -->

<!-- **Epic Jira**: [JIRA_KEY-XXXX](https://JIRA_DOMAIN/browse/JIRA_KEY-XXXX) -->

<!-- | Key | Tarea | Estado | -->
<!-- |---|---|---| -->
<!-- | [JIRA_KEY-XXXX](url) | Descripción | Por hacer / En curso / Hecho | -->

## Gaps detectados

<!-- Claude completa esto al revisar la iniciativa. Formato obligatorio para el dashboard: -->
<!-- - [ ] Gap abierto: descripción                                                       -->
<!-- - [x] Gap cerrado: descripción — resolución                                          -->

=== FIN: templates/initiative.md ===

**4b. Escribir `{BASE_PATH}/management/templates/resources.md`:**

=== INICIO: templates/resources.md ===

# Recursos — [Nombre de la Iniciativa]

## Documentos

<!-- Un ítem por Drive. Formato:                                              -->
<!-- - [Nombre del doc](https://docs.google.com/...) — descripción breve     -->

## Figma

<!-- - [Nombre del frame](https://figma.com/...) — descripción -->

## Slack

<!-- Un ítem por canal.          -->
<!-- - `#nombre-canal` — propósito -->

## Reuniones

<!-- - *Por documentar* -->

## Links útiles

<!-- - [Nombre del doc](url)                           -->
<!-- - Jira Epic: [JIRA_KEY-XXXX](https://JIRA_DOMAIN/browse/JIRA_KEY-XXXX) -->

## Contactos

Ver `dependencies.md` — los contactos están organizados por equipo y estado de dependencia.

=== FIN: templates/resources.md ===

**4c. Escribir `{BASE_PATH}/management/templates/dependencies.md`:**

=== INICIO: templates/dependencies.md ===

# Dependencias — [Nombre de la Iniciativa]

## Equipos externos

<!-- Columnas: Equipo | Contactos | Qué depende | Estado -->

| Equipo | Contactos | Qué depende | Estado |
|---|---|---|---|

## Notas

<!-- Detallar conversaciones, acuerdos, riesgos de timeline -->

## Equipos a monitorear

<!-- Equipos cuyo trabajo puede impactar esta iniciativa sin ser dependencia técnica directa -->

| Equipo | Contactos | Qué depende | Estado |
|---|---|---|---|

## Iniciativas internas

| Iniciativa | Relación | Estado | Notas |
|---|---|---|---|

=== FIN: templates/dependencies.md ===

---

### Paso 5: Escribir skeleton files

**5a. Escribir `{BASE_PATH}/management/team.md`:**

=== INICIO: team.md ===

# Team — TEAM_NAME

## Integrantes

| Nombre | Perfil | Notas |
|---|---|---|

<!-- Completar con los miembros del equipo. El campo Nombre debe ser el nombre exacto     -->
<!-- que se usará en todos los archivos de iniciativas como Propietario y Devs asignados. -->

## Capacidad

<!-- Notas sobre disponibilidad, vacaciones, licencias, proyectos externos. -->

=== FIN: team.md ===

**5b. Escribir `{BASE_PATH}/management/inbox.md`:**

=== INICIO: inbox.md ===

# Inbox — TEAM_NAME

Captura rápida de lo que llega. Sin formato, sin presión.
Cuando algo sea claramente parte de una iniciativa, moverlo a la sección de notas crudas correspondiente.

---

<!-- Agregar entradas con fecha. Ejemplo:                                    -->
<!-- - YYYY-MM-DD: [descripción de lo que llegó]                             -->

=== FIN: inbox.md ===

**5c. Escribir `{BASE_PATH}/management/initiatives/_index.md`:**

=== INICIO: initiatives/_index.md ===

# Índice de Iniciativas — TEAM_NAME

## CURRENT_QUARTER

| Slug | Nombre | Estado | Prioridad | Propietario | Fecha |
|---|---|---|---|---|---|

### Cerradas

<!-- Iniciativas cerradas o canceladas en este quarter -->

---

## Estados

| Estado | Descripción |
|---|---|
| borrador | Información inicial, todavía incompleta |
| en refinamiento | Se está completando activamente |
| planificada | Epics y stories listos |
| en ejecucion | Devs trabajando |
| bloqueada | Esperando dependencia externa |
| cerrada | Done o cancelada |

=== FIN: initiatives/_index.md ===

---

### Paso 6: Instalar skill init-initiative

Escribir el siguiente contenido en `{BASE_PATH}/management/.claude/skills/init-initiative/SKILL.md`:

=== INICIO: .claude/skills/init-initiative/SKILL.md ===

---
name: init-initiative
description: Crea una nueva iniciativa con scaffolding completo desde templates. Valida formato, nombres de team.md, y actualiza _index.md. Usar cuando se necesite crear una nueva iniciativa.
argument-hint: [nombre-iniciativa]
user-invocable: true
---

# Init Initiative

Scaffolding automatizado de nuevas iniciativas.

---

## Cuando usar

Invocar cuando se necesite crear una nueva iniciativa. Reemplaza el proceso manual de copiar templates, crear carpetas y agregar a _index.md.

---

## Flujo obligatorio

Seguir estos pasos en orden estricto. No saltear ninguno.

### Paso 1: Obtener nombre

Si el usuario pasó un argumento, usarlo como nombre de la iniciativa. Si no, preguntar:

> Nombre de la iniciativa (obligatorio):

### Paso 2: Generar slug

Derivar un slug del nombre:
- Lowercase
- Espacios y underscores a guiones
- Remover acentos (a/e/i/o/u sin tilde)
- Remover emojis y caracteres especiales
- Remover artículos cortos (de, del, la, el, los, las) cuando no sean significativos
- Máximo 5 palabras separadas por guion

Mostrar el slug generado y preguntar si está bien o quiere otro.

### Paso 3: Datos opcionales

Preguntar al usuario (puede saltear todo con Enter):

1. Jira Epic key (ej: PROYECTO-1850) — opcional
2. Propietario — opcional, debe ser nombre exacto de team.md
3. Contexto inicial — texto libre opcional

### Paso 4: Validaciones

Antes de crear cualquier archivo, validar:

4a. Slug único
- Leer el contenido del directorio initiatives/
- Si ya existe una carpeta con ese slug: ABORTAR con mensaje de error

4b. Propietario válido (solo si se dio uno)
- Leer team.md
- Extraer la columna Nombre de la tabla de integrantes
- El nombre dado debe coincidir exactamente con uno de esos nombres
- Si no coincide: mostrar los nombres válidos y pedir que elija uno

### Paso 5: Crear archivos

5a. Resolver URL de Jira (solo si se dio un Jira Epic key)
- Leer CLAUDE.md sección ## Configuracion
- Extraer Jira base URL y Jira project key
- Formatear link como: [{KEY}](https://{JIRA_DOMAIN}/browse/{KEY})
- Si no hay configuración de Jira en CLAUDE.md: usar solo el KEY sin link

5b. initiatives/{slug}/initiative.md
Leer templates/initiative.md. Reemplazar:
- [Nombre de la Iniciativa] en el título con el nombre real
- Dejar el header blockquote con los valores correctos

5c. initiatives/{slug}/resources.md
Leer templates/resources.md. Reemplazar [Nombre de la Iniciativa] con el nombre real.
Si hay Jira Epic, agregar en ## Links útiles.

5d. initiatives/{slug}/dependencies.md
Leer templates/dependencies.md. Reemplazar [Nombre de la Iniciativa] con el nombre real.

5e. initiatives/{slug}/docs/.gitkeep
Crear archivo vacío para que la carpeta docs/ exista.

### Paso 6: Actualizar _index.md

Leer initiatives/_index.md. Agregar una nueva fila al final de la tabla del quarter activo:
| {slug} | [{nombre}](./{slug}/initiative.md) | borrador | - | {propietario o -} | {YYYY-MM-DD de hoy} |

### Paso 7: Reporte

Mostrar resumen de lo creado con próximos pasos sugeridos.

---

## Reglas

- No inventar información. Dejar placeholders del template si el usuario no da contexto.
- Formato blockquote obligatorio. El header SIEMPRE debe usar > **Campo**: valor.
- Nombres exactos. El propietario debe coincidir exactamente con team.md.
- No modificar templates. Solo leerlos como base.
- Fecha automática. Usar la fecha del día en formato YYYY-MM-DD.
- Jira URL de CLAUDE.md. Nunca hardcodear la URL de Jira. Siempre leerla de ## Configuracion en CLAUDE.md.

=== FIN: .claude/skills/init-initiative/SKILL.md ===

---

### Paso 7: Instalar skill ingest-doc

Escribir el siguiente contenido en `{BASE_PATH}/management/.claude/skills/ingest-doc/SKILL.md`:

=== INICIO: .claude/skills/ingest-doc/SKILL.md ===

---
name: ingest-doc
description: Busca la última versión de documentos en Google Drive relacionados con una iniciativa, extrae información estructurada, y propone actualizaciones a initiative.md, resources.md y dependencies.md. Previsualización obligatoria antes de escribir.
argument-hint: [slug-iniciativa]
user-invocable: true
---

# Ingest Doc

Ingesta automatizada de documentos desde Google Drive para iniciativas.
Nunca escribe sin aprobación del usuario.

---

## Flujo obligatorio

### Paso 1: Resolver iniciativa

Si el usuario pasó un argumento, usarlo como slug.
Si no: leer _index.md, listar iniciativas no cerradas, preguntar cuál procesar.
Validar que initiatives/{slug}/ existe. Si no: ABORTAR.

### Paso 2: Leer estado actual

Leer: initiatives/{slug}/initiative.md, resources.md, dependencies.md.
Extraer nombre e iniciativa y palabras clave para búsqueda.

### Paso 3: Buscar documentos en Google Drive

Usar gdrive-reader para buscar documentos relacionados.
Buscar por nombre de la iniciativa o palabras clave del título.
Presentar lista al usuario y preguntar cuáles procesar.
Si no se encuentran: preguntar si el usuario quiere proporcionar un link directo.

### Paso 4: Clasificar y extraer información

Clasificar cada documento como: PRD, RFC, Kick-off, Planilla, Notas de reunión.
Para cada tipo, extraer: contexto, alcance, dependencias, timeline, decisiones.

### Paso 5: Generar propuesta

Mostrar SOLO lo nuevo o lo que cambia (no repetir contenido existente).
Incluir secciones: Contexto, Timeline, Alcance, Dependencies, Resources, Notas crudas, Gaps.
Atribuir fuente para datos específicos.

### Paso 6: Scope guard y risk patterns

6a. Scope guard: leer sección 🧭 Identidad del equipo en CLAUDE.md.
Alertar si algún contenido extraído está fuera del scope del equipo.

6b. Preguntas críticas por tipo (migración / apagado / cross / campaña):
Agregar como gap cualquier pregunta crítica que no tenga respuesta en los documentos.

6c. Risk patterns: leer sección ## Risk Patterns en CLAUDE.md.
Si existe y la iniciativa toca esos dominios, agregar alertas.

### Paso 7: Pedir aprobación

Mostrar propuesta completa + alertas.
Opciones: Aplicar / Editar / Cancelar.
No escribir nada sin aprobación explícita.

### Paso 8: Escribir cambios

Con aprobación, actualizar archivos con merge (nunca overwrite).
Secciones actualizables en initiative.md: Contexto, Notas crudas, Alcance, Gaps detectados.
Actualizar resources.md y dependencies.md.
Sugerir cambio de estado si se agregó contenido sustancial.

### Paso 9: Reporte final

Mostrar resumen: documentos procesados, secciones actualizadas, gaps detectados, alertas.

---

## Reglas

- No inventar información. Solo extraer lo que dice el documento.
- Merge, no overwrite.
- Atribuir fuente para datos específicos.
- Preview obligatorio. Nunca escribir sin aprobación.
- Nombres exactos. Validar miembros del equipo contra team.md.
- Fecha automática en formato YYYY-MM-DD.

=== FIN: .claude/skills/ingest-doc/SKILL.md ===

---

### Paso 8: Reporte final

Mostrar resumen de lo creado:

```
Management Center creado en {BASE_PATH}/management/

Archivos creados:
  management/CLAUDE.md                           ✓
  management/team.md                             ✓
  management/inbox.md                            ✓
  management/initiatives/_index.md               ✓
  management/templates/initiative.md             ✓
  management/templates/resources.md              ✓
  management/templates/dependencies.md           ✓
  management/.claude/skills/init-initiative/     ✓
  management/.claude/skills/ingest-doc/          ✓

Configuración:
  Equipo:   {TEAM_NAME}
  Jira key: {JIRA_KEY}
  Quarter:  {CURRENT_QUARTER}

Próximos pasos:
  1. Completar management/team.md con los miembros del equipo
  2. Completar la sección "🧭 Identidad del equipo" en CLAUDE.md
     (qué hace y qué NO hace el equipo)
  3. Agregar risk patterns en CLAUDE.md sección ## Risk Patterns
  4. Abrir Claude Code desde management/ para usar /init-initiative e /ingest-doc
```

---

## Reglas del skill

- **Confirmación antes de ejecutar.** Mostrar resumen de parámetros y esperar OK antes del Paso 2.
- **No modificar archivos existentes.** Si `management/` ya existe en el destino, preguntar si continuar o abortar.
- **Placeholders reemplazados.** Ningún archivo final debe contener los strings TEAM_NAME, TEAM_MISSION, JIRA_KEY, JIRA_DOMAIN, CURRENT_QUARTER — todos deben estar sustituidos con los valores reales.
- **Si Jira es "none":** reemplazar referencias de Jira en CLAUDE.md y templates con "N/A" o comentarios indicando que no aplica.
