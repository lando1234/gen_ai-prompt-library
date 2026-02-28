# Management Center Kit

Sistema de skills para Claude Code que bootstrapea un Management Center completo para equipos de ingeniería: gestión de iniciativas, planificación trimestral, carga del equipo y actualizaciones ejecutivas.

## Componentes

| Skill | Tipo | Descripción |
|---|---|---|
| `setup-management` | One-time | Crea toda la arquitectura de `management/` |
| `init-initiative` | Runtime | Scaffolding de nuevas iniciativas |
| `ingest-doc` | Runtime | Ingesta documentos desde Google Drive |

## Instalación en máquina nueva

```bash
# 1. Clonar este repo
git clone [repo-url]
cd gen_ai-prompt-library

# 2. Instalar setup-management globalmente
cp -r skills/management-center/setup-management ~/.claude/skills/

# 3. Ir al workspace donde querés crear management/
cd ~/tu-workspace

# 4. Abrir Claude Code y ejecutar
/setup-management
```

El skill hace 5 preguntas y crea todo — incluyendo instalar `init-initiative` e `ingest-doc` en el proyecto.

## Actualizar runtime skills en proyecto existente

Si querés actualizar init-initiative o ingest-doc sin volver a hacer setup:

```bash
cp -r skills/management-center/init-initiative [workspace]/management/.claude/skills/
cp -r skills/management-center/ingest-doc [workspace]/management/.claude/skills/
```

> **Nota de sincronización**: `setup-management` embebe el contenido de ambos runtime skills. Si actualizás `init-initiative` o `ingest-doc`, reflejá también los cambios en las secciones `---INIT-INITIATIVE---` y `---INGEST-DOC---` dentro de `setup-management/SKILL.md`.

## Qué genera el setup

```
management/
├── CLAUDE.md              ← 11 workflows + state machine + quarter rules
├── team.md                ← tabla vacía (completar después)
├── inbox.md               ← captura rápida
├── initiatives/
│   └── _index.md          ← tabla + sección del quarter inicial
├── templates/
│   ├── initiative.md
│   ├── resources.md
│   └── dependencies.md
└── .claude/
    └── skills/
        ├── init-initiative/SKILL.md
        └── ingest-doc/SKILL.md
```

## Parámetros del setup

| Pregunta | Ejemplo |
|---|---|
| Nombre del equipo | "Platform Engineering" |
| Misión en una línea | "Proveemos la infraestructura core que..." |
| Jira project key | "PLATFORM" (o "none") |
| Jira domain | "mycompany.atlassian.net" (o "none") |
| Quarter actual | "Q1-2026" |

## Qué no incluye (negocio-específico)

Estas cosas se completan manualmente después del setup:

- **Miembros del equipo** → `management/team.md`
- **Risk patterns por dominio** → `management/CLAUDE.md` sección `## Risk Patterns`
- **Scope guard específico** → `management/CLAUDE.md` sección identidad del equipo
- **Daily sync automation** → requiere configuración de MCP Atlassian propia
- **Dashboard web** → si usás un dashboard personalizado, requiere setup aparte
