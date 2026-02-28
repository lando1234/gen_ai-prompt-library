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

Invocar cuando se necesite crear una nueva iniciativa. Reemplaza el proceso manual de copiar templates, crear carpetas y agregar a `_index.md`.

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

1. **Jira Epic key** (ej: PROYECTO-1850) — opcional
2. **Propietario** — opcional, debe ser nombre exacto de `team.md`
3. **Contexto inicial** — texto libre opcional

### Paso 4: Validaciones

Antes de crear cualquier archivo, validar:

**4a. Slug único**
- Leer el contenido del directorio `initiatives/`
- Si ya existe una carpeta con ese slug: **ABORTAR** con mensaje de error

**4b. Propietario válido** (solo si se dio uno)
- Leer `team.md`
- Extraer la columna `Nombre` de la tabla de integrantes
- El nombre dado debe coincidir **exactamente** con uno de esos nombres
- Si no coincide: mostrar los nombres válidos y pedir que elija uno

### Paso 5: Crear archivos

Leer cada template y crear los archivos con los placeholders reemplazados.

**5a. Resolver URL de Jira** (solo si se dio un Jira Epic key)
- Leer `CLAUDE.md` sección `## Configuracion`
- Extraer `Jira base URL` y `Jira project key`
- Formatear link como: `[{KEY}](https://{JIRA_DOMAIN}/browse/{KEY})`
- Si no hay configuración de Jira en CLAUDE.md: usar solo el KEY sin link

**5b. `initiatives/{slug}/initiative.md`**

Leer `templates/initiative.md`. Reemplazar:
- `[Nombre de la Iniciativa]` en el título (línea 1) con el nombre real
- Dejar el header blockquote exactamente así (reemplazando solo los valores):

```
> **Estado**: borrador
> **Prioridad**: -
> **Propietario**: {nombre del propietario o -}
> **Jira Epic**: {link formateado o -}
> **Recursos**: Ver [`resources.md`](./resources.md)
> **Dependencias**: Ver [`dependencies.md`](./dependencies.md)
```

Si hay contexto inicial, escribirlo debajo del header `## Contexto` (reemplazando el comentario HTML placeholder).

**5c. `initiatives/{slug}/resources.md`**

Leer `templates/resources.md`. Reemplazar:
- `[Nombre de la Iniciativa]` en el título con el nombre real

Si hay Jira Epic, agregar en la sección `## Links útiles`:
- `- Jira Epic: [{KEY}](https://{JIRA_DOMAIN}/browse/{KEY})`

**5d. `initiatives/{slug}/dependencies.md`**

Leer `templates/dependencies.md`. Reemplazar:
- `[Nombre de la Iniciativa]` en el título con el nombre real

**5e. `initiatives/{slug}/docs/.gitkeep`**

Crear archivo vacío para que la carpeta `docs/` exista.

### Paso 6: Actualizar _index.md

Leer `initiatives/_index.md`. Agregar una nueva fila **al final de la tabla del quarter activo** (antes de cualquier separador o sección de estados):

```
| {slug} | [{nombre}](./{slug}/initiative.md) | borrador | - | {propietario o -} | {YYYY-MM-DD de hoy} |
```

### Paso 7: Reporte

Mostrar resumen de lo creado:

```
Iniciativa "{nombre}" creada:

  initiatives/{slug}/
  ├── initiative.md
  ├── resources.md
  ├── dependencies.md
  └── docs/

Entrada agregada a _index.md con estado: borrador

Próximos pasos sugeridos:
- Volcar documentos en docs/ con /ingest-doc
- Completar secciones de initiative.md manualmente
- Cuando haya suficiente info, pasar a "en refinamiento"
```

---

## Reglas

- **No inventar información.** Si el usuario no da contexto, dejar las secciones con los placeholders del template.
- **Formato blockquote obligatorio.** El header SIEMPRE debe usar `> **Campo**: valor`. Nunca `**Campo**: valor` sin blockquote.
- **Nombres exactos.** El propietario debe coincidir exactamente con `team.md`. No aceptar diminutivos.
- **No modificar templates.** Los templates en `templates/` son read-only. Solo leerlos como base.
- **Fecha automática.** Usar la fecha del día en formato YYYY-MM-DD para `_index.md`.
- **Jira URL de CLAUDE.md.** Nunca hardcodear la URL de Jira. Siempre leerla de `## Configuracion` en `CLAUDE.md`.
