# SpecKit - Spec-Driven Development

Spec-Driven Development is a structured process that emphasizes intent-driven development, rich specification creation, multi-step refinement, and heavy reliance on AI for specification interpretation.

## Workflow

The SpecKit workflow follows these phases in order:

1. **Constitution** — Define project governing principles and development guidelines (`.specify/memory/constitution.md`)
2. **Specify** — Define WHAT to build and WHY, focusing on requirements and user stories (`specs/{feature}/spec.md`)
3. **Plan** — Create technical implementation plan with tech stack and architecture (`specs/{feature}/plan.md`)
4. **Tasks** — Break plan into actionable, ordered tasks (`specs/{feature}/tasks.md`)
5. **Implement** — Execute tasks in order, validating each one

## How to use

When the user says they want to build a feature using SpecKit:

1. First run `/speckit.constitution` if no constitution exists yet
2. Ask the user what they want to build, then run `/speckit.specify`
3. Ask about tech stack preferences, then run `/speckit.plan`
4. Run `/speckit.tasks` to generate task breakdown
5. Run `/speckit.implement` to execute

## Structure
- `.specify/memory/constitution.md` - Project principles
- `.specify/templates/` - Templates for spec, plan, tasks
- `specs/{feature-name}/` - Feature specifications
