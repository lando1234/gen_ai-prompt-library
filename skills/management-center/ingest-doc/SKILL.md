---
name: ingest-doc
description: Busca la última versión de documentos en Google Drive relacionados con una iniciativa, extrae información estructurada, y propone actualizaciones a initiative.md, resources.md y dependencies.md. Previsualización obligatoria antes de escribir.
argument-hint: [slug-iniciativa]
user-invocable: true
---

# Ingest Doc

Ingesta automatizada de documentos desde Google Drive para iniciativas del equipo.

Busca documentos relevantes en Drive, clasifica su contenido, extrae información estructurada, aplica scope guard y risk patterns, y propone actualizaciones a los archivos de la iniciativa. Nunca escribe sin aprobación.

---

## Cuando usar

Invocar cuando haya documentos nuevos o actualizados en Google Drive relacionados con una iniciativa (PRDs, RFCs, notas de kick-off, planillas de campañas, etc.). Reemplaza el proceso manual de abrir cada doc, extraer información, y volcarlo a las secciones correctas.

---

## Flujo obligatorio

Seguir estos pasos en orden estricto. No saltear ninguno.

### Paso 1: Resolver iniciativa

Si el usuario pasó un argumento, usarlo como slug.

Si no pasó argumento:
1. Leer `initiatives/_index.md`
2. Listar iniciativas que NO estén en estado "cerrada"
3. Preguntar cuál quiere procesar

Validar que `initiatives/{slug}/` existe. Si no existe: **ABORTAR** con mensaje:

> La iniciativa `{slug}` no existe. Usá `/init-initiative` para crearla primero.

### Paso 2: Leer estado actual completo

Leer estos archivos:
- `initiatives/{slug}/initiative.md`
- `initiatives/{slug}/resources.md`
- `initiatives/{slug}/dependencies.md`

Extraer del `initiative.md`:
- Nombre de la iniciativa (título H1)
- Palabras clave relevantes para la búsqueda (nombre, beneficios mencionados, apps)

### Paso 3: Buscar documentos en Google Drive

Usar la skill `gdrive-reader` para buscar documentos relacionados con la iniciativa.

**Estrategia de búsqueda:**
1. Buscar por el nombre de la iniciativa (o palabras clave del título)
2. Si la búsqueda devuelve pocos resultados, intentar con términos más amplios
3. Si hay links a docs de Drive ya registrados en `resources.md`, incluirlos directamente

**Filtros de búsqueda:**
- Priorizar documentos modificados recientemente (último mes)
- Tipos relevantes: Google Docs, Google Sheets, Google Slides, PDFs

**Presentar al usuario la lista de documentos encontrados:**

```
Documentos encontrados en Drive para "{nombre de la iniciativa}":

1. {nombre} — {tipo} — Modificado: {fecha} — {link}
2. {nombre} — {tipo} — Modificado: {fecha} — {link}
...

¿Cuáles querés procesar? (todos / lista de números / ninguno)
```

Si el usuario dice "todos": procesar todos.
Si indica números: procesar solo esos.
Si dice "ninguno" o cancela: **ABORTAR**.

Si no se encuentran documentos: preguntar si quiere proporcionar un link directo o término alternativo. Si no hay nada: **ABORTAR** con mensaje:

> No se encontraron documentos en Drive para esta iniciativa. Podés proporcionar un link directo o un término de búsqueda alternativo.

### Paso 4: Leer y clasificar cada documento

Leer el contenido de cada documento seleccionado usando `gdrive-reader`.

Clasificar cada documento en una de estas categorías y extraer la información correspondiente:

**PRD (Product Requirements Document)**
- Contexto de negocio
- Alcance (dentro y fuera de scope)
- KPIs / métricas esperadas
- Riesgos identificados
- Timeline / hitos

**RFC (Request for Comments / Technical)**
- Contexto técnico
- Alcance técnico
- Dependencias técnicas
- Equipos involucrados
- Endpoints / APIs relevantes

**Kick-off / Presentación**
- Contexto de negocio
- Timeline / hitos
- Stakeholders
- Decisiones tomadas

**Planilla (Google Sheets / Excel)**
- Tablas de datos relevantes
- Configuraciones (límites, montos, fechas)
- IDs de recursos

**Notas de reunión**
- Decisiones tomadas
- Action items
- Contexto adicional

Si un archivo no se puede clasificar en ninguna categoría: marcarlo para ir a "Notas crudas" con una nota indicando que no se pudo clasificar automáticamente.

### Paso 5: Generar propuesta de actualización

Cruzar lo extraído de los documentos contra lo que ya existe en `initiative.md`, `resources.md` y `dependencies.md`.

Producir una propuesta mostrando **SOLO lo que cambia o se agrega** (no repetir contenido existente). Formato:

````
## Propuesta de ingesta — {nombre de la iniciativa}

**Documentos procesados**: {N} archivos
**Tipos detectados**: {lista de tipos}

### Contexto (agregar/actualizar):
{texto propuesto, con atribución de fuente}

### Timeline clave (agregar):
- **DD/MM/YYYY**: {hito} *(fuente: {nombre del doc})*

### Alcance > Dentro de scope (agregar):
- {item 1} *(fuente: {nombre del doc})*

### Alcance > Fuera de scope (agregar):
- {item} *(fuente: {nombre del doc})*

### Dependencies (agregar a dependencies.md):
| Equipo | Contactos | Qué depende | Estado |
|---|---|---|---|
| {equipo} | {contacto si se menciona} | {qué depende} | pendiente |

### Resources (agregar a resources.md):
- [{nombre del doc}]({link de Drive}) — {descripción breve del contenido}

### Notas crudas (agregar):
- {YYYY-MM-DD}: {información no clasificada o fragmentos sueltos}

### Gaps detectados:
- [ ] Gap abierto: {descripción}
````

**Reglas para la propuesta**:
- Atribuir fuente: cuando se extraen datos específicos (tablas, IDs, métricas, fechas), indicar de qué documento vienen.
- Si una sección no tiene nada nuevo que agregar, omitirla de la propuesta.
- Si algo ya existe en initiative.md, NO repetirlo.
- El contexto debe ser un resumen coherente, no un copy-paste de fragmentos.

### Paso 6: Scope guard y risk patterns

**6a. Scope guard del equipo**

Leer la sección `## 🧭 Identidad del equipo` en `CLAUDE.md` para determinar qué está fuera de scope del equipo (campos "NO hacemos" o equivalente).

Revisar si la información extraída incluye algo fuera de ese scope. Formato de alerta si hay algo fuera:

```
### Alertas de scope
- **OUT OF SCOPE**: {sección} define {tema}. Esto es responsabilidad de {equipo/rol}. Documentar pero no incluir como trabajo propio.
```

**6b. Preguntas críticas según tipo de iniciativa**

Detectar el tipo de iniciativa desde el contenido y aplicar las preguntas correspondientes. Si alguna pregunta NO tiene respuesta en los documentos, agregarla como gap:

**Si es una migración:**
- [ ] Gap: No queda claro si legacy y nuevo conviven o es cutover
- [ ] Gap: No se define plan de rollback
- [ ] Gap: No se menciona qué sucede con datos en vuelo
- [ ] Gap: No hay timeline de apagado definitivo del legacy

**Si es un apagado:**
- [ ] Gap: No se identifican dependencias de otros equipos
- [ ] Gap: No hay plan de comunicación
- [ ] Gap: No se menciona migración de elementos afectados

**Si es una iniciativa cross (multi-dominio):**
- [ ] Gap: No se listan los dominios incluidos/excluidos explícitamente
- [ ] Gap: No hay owner claro por dominio
- [ ] Gap: No queda claro si los dominios tienen dependencias entre sí

**Si es configuración de campaña:**
- [ ] Gap: No se define quién valida los límites/CAPs
- [ ] Gap: No se especifica cuándo se enciende/apaga
- [ ] Gap: No se menciona convivencia con configuraciones legacy

Solo agregar gaps para preguntas que efectivamente NO tienen respuesta en los documentos.

**6c. Risk patterns por dominio**

Leer la sección `## Risk Patterns` en `CLAUDE.md`. Si existe y la iniciativa toca alguno de los dominios listados, agregar las alertas correspondientes.

Si no hay risk patterns documentados en `CLAUDE.md`, omitir este paso.

Formato:
```
### Alertas de riesgo por dominio
- **{Dominio}**: {descripción del riesgo}. Verificar con el equipo.
```

### Paso 7: Pedir aprobación

Mostrar al usuario:
1. La propuesta completa del Paso 5
2. Las alertas de scope del Paso 6a (si hay)
3. Los gaps adicionales del Paso 6b (si hay)
4. Las alertas de riesgo del Paso 6c (si hay)

Pedir confirmación con estas opciones:
- **Aplicar**: Escribir todos los cambios propuestos
- **Editar**: El usuario indica qué ajustar, se modifica la propuesta y se vuelve a mostrar
- **Cancelar**: No escribir nada

**No escribir nada sin aprobación explícita.**

### Paso 8: Escribir cambios

Con aprobación, actualizar los archivos. Respetar formato de templates.

**8a. `initiative.md`**

Merge, no overwrite. Para cada sección con cambios:
- Si la sección está vacía (solo tiene comentario HTML): reemplazar el comentario con el contenido nuevo
- Si la sección ya tiene contenido: agregar al final del contenido existente, separado por un salto de línea

Secciones que se pueden actualizar:
- `## Contexto` — agregar/actualizar texto y timeline
- `## Notas crudas` — agregar al final con fecha
- `## Alcance > Dentro de scope` — agregar items
- `## Alcance > Fuera de scope` — agregar items
- `## Gaps detectados` — agregar gaps nuevos

El header blockquote (`> **Estado**: ...`) NO se toca a menos que se sugiera cambio de estado.

**8b. `resources.md`**

En la sección correspondiente a documentos, agregar una línea por cada documento procesado con su link de Drive:
```
- [{nombre del doc}]({link de Drive}) — {descripción breve}
```
No duplicar entradas que ya existan.

**8c. `dependencies.md`**

En la tabla de `## Equipos externos`, agregar filas para equipos detectados que no estén ya en la tabla.

**8d. Nota de ingesta**

Agregar en `## Notas crudas` de initiative.md:
```
- {YYYY-MM-DD}: Ingesta de {N} documentos desde Drive via /ingest-doc
```

**8e. Sugerir cambio de estado**

Si la iniciativa está en "borrador" y se agregó contenido sustancial (contexto + alcance como mínimo), sugerir:

> La iniciativa tiene suficiente contenido para pasar a "en refinamiento". ¿Querés que actualice el estado?

Si el usuario acepta: actualizar el campo `Estado` en el header blockquote de initiative.md Y la columna `Estado` en `_index.md`.

### Paso 9: Reporte final

Mostrar resumen de lo escrito:

```
Ingesta completada para "{nombre}":

  Documentos procesados: {N}
  Secciones actualizadas en initiative.md: {lista}
  Dependencias agregadas: {N nuevas}
  Resources agregados: {N nuevos}
  Gaps detectados: {N}
  Alertas de scope: {N}
```

---

## Reglas

- **No inventar información.** Solo extraer lo que dice el documento. Si algo no está, marcarlo como gap. Nunca rellenar con suposiciones.
- **Merge, no overwrite.** Si una sección ya tiene contenido, agregar al final. Nunca reemplazar lo existente.
- **Atribuir fuente.** Cuando se extraen datos específicos (tablas, IDs, métricas, fechas), indicar de qué documento de Drive vienen.
- **Preview obligatorio.** Nunca escribir sin aprobación del usuario. Siempre mostrar propuesta primero.
- **Formato blockquote.** Si se toca el header, mantener formato `> **Campo**: valor`.
- **Nombres exactos.** Si se mencionan miembros del equipo, validar contra `team.md` y usar el nombre exacto.
- **Fecha automática.** Usar la fecha del día en formato YYYY-MM-DD para notas y entradas.
- **Links de Drive, no rutas locales.** Los resources se guardan como links clickeables, no como rutas de archivo.
