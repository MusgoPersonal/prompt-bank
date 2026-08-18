# Banco de Prompts y Skills

Banco personal de **prompts reutilizables** y **skills de opencode** usados para construir webs
inmersivas, escenas 3D scroll-driven y experiencias premium (nivel Awwwards).

## Estructura

```
prompts/   → plantillas de prompt reutilizables (el principal es el brief premium)
briefs/    → briefs reales de clientes (para alimentar el template)
skills/    → skills de opencode listos para copiar a ~/.config/opencode/skills/
```

## 📌 El más importante: `prompts/brief-premium.md`

Template **Brief Premium Reutilizable** para construir webs inmersivas con protagonista 3D.
Arco narrativo de 8 a 12 escenas, cámara cinematográfica, atmósferas sincronizadas con el
scroll, y prioridades: WOW visual → Narrativa → Fluidez → Calidad 3D.

### Cómo usarlo

1. Abre `prompts/brief-premium.md`.
2. Rellena la sección `VARIABLES DEL PROYECTO` (marca, sector, tagline, protagonista 3D,
   paleta, referencias estéticas, número de escenas).
3. Toma el brief del cliente (ver `briefs/`) y completa las variables con sus datos.
4. Pásale el prompt completo al agente para construir la web.

Los briefs de `briefs/` son la fuente de datos reales (clientes, target, paletas, objetivos).

## Skills

Cada carpeta en `skills/` es un skill de opencode (formato `SKILL.md`). Para instalarlo en tu
máquina:

```powershell
Copy-Item skills/<nombre> "$HOME\.config\opencode\skills\<nombre>" -Recurse
# Reinicia opencode para que cargue el skill.
```

| Skill | Descripción |
|---|---|
| `3d-scene-workflow` | Workflow para crear/mejorar/revisar escenas 3D scroll-driven: brief → máquina de estados (xMachine) → keyframes de cámara/atmósfera → verificación. Referencia a `prompt-source.md`. |
| `spec-kit` | Trabajo orientado a especificaciones. |
| `obsidian-skills` | Bundle de skills para trabajar con vaults de Obsidian (markdown, bases, canvas, CLI). |
| `codebase-memory` | Consultas estructurales del código vía grafo de conocimiento (depende del MCP codegraph). |

## Notas

- Todos los archivos son UTF-8.
- Los briefs de clientes son información sensible: este repo es privado, no lo hagas público.
