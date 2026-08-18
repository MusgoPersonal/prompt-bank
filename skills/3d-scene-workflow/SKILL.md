---
name: 3d-scene-workflow
description: Flujo de trabajo para crear, mejorar y revisar escenas 3D scroll-driven (react-three) en los proyectos sanabria-web, urbancore-web y el paquete urban-scenes-shared. Use when the user asks to crear/mejorar/revisar una escena 3D, aplicar el Template Web Inmersiva / prompt-brief premium, trabajar con máquinas de estados (xMachine), keyframes de cámara/atmósfera, o registrar una escena por service.id. Trigger keywords: escena 3d, scene, react-three, react three fiber, brief, barber, house, remodel, storefront, repair, office, urban-scenes-shared.
---

# Flujo de trabajo de escenas 3D

Workflow completo para **crear**, **mejorar** y **revisar** escenas 3D scroll-driven en los
proyectos del usuario. Va desde el brief premium (narrativa) hasta la implementación y la
verificación, pasando por el contrato de arquitectura.

## Fuentes del workflow

1. **`prompt-source.md`** (en esta misma carpeta) — el *Template Web Inmersiva*: brief premium
   reutilizable (protagonista 3D, 11 secciones, atmósferas, dirección de animación, responsive,
   rendimiento, accesibilidad). **Se referencia, no se incrusta en este skill.**
2. **Contrato de escenas** (abajo) — reglas de arquitectura de código derivadas del proyecto
   `urban-scenes-shared` y los README de `urbancore-web/src/scenes`.

## Contrato de escenas

Toda escena debe cumplir estas reglas:

- **Máquina pura**: la lógica de progreso vive en `xMachine.ts` como funciones `derive*` que
  convierten progreso (0–1) en valores por fase. **Sin imports de React/tres en la máquina.**
- **Test por máquina**: `xMachine.test.ts` con vitest; cubrir fases, transiciones y límites.
- **Componente `useFrame`**: la escena (`XScene.tsx`) lee el progreso y mueve objetos/cámara;
  nunca muta nada fuera del frame loop.
- **Keyframes**: cámara (`CAMERA`) y atmósfera (`ATMOSPHERE`) como paths de keyframes (una por
  fase) con `pos`, `target`, `fov` y paleta/colores.
- **Infra compartida**: todo lo común importa de `urban-scenes-shared` (SceneCanvas,
  SceneSection, CameraRig, SceneAtmosphere, AmbientDust, Effects, hooks). **Nunca duplicar**
  infra dentro de una escena ni en el proyecto.
- **Posicionamiento por origen**: los objetos se modelan relativos al origen; el `group` se
  posiciona en el centro de la escena y rota/flota sobre su propio eje (nunca rotar partículas
  alrededor de un centro distante). Ver `AmbientDust`.
- **Registro**: la escena se registra por `service.id` en `Servicios.tsx`
  (`sceneByService`/`cameraByService`). Sin registro, la sección no muestra nada.
- **Mobile / reduced-motion**: bajar calidad en móvil y respetar `prefers-reduced-motion`
  (hooks del paquete compartido).
- **Sanidad**: sin dead code, sin imports huérfanos, sin duplicación, 60 FPS.

## Mapa de proyectos y escenas

| Proyecto | Escena(s) | Patrón |
|---|---|---|
| `../sanabria-web` | `src/scenes/barber/*` — `BarberScene` (silla de barbero, canvas único continuo, 11 fases `barberMachine`) | Canvas único detrás de toda la página + `useGlobalProgress` + overlay de secciones |
| `../urbancore-web` | `src/scenes/{house,remodel,storefront,repair,office}/*` — una por servicio (`sceneByService`) | `SceneSection` fija por sección + progreso propio `useSectionProgress` |
| `../urban-scenes-shared` | Paquete compartido (fuente única de infra) | Compilado a `dist/` con `prepare` (tsc); consumido vía `"file:..."` |
| `../banco-animaciones` | Repositorio de animaciones (copias históricas autocontenidas) | Referencia, no fuente |

Notas clave:

- `barber` es **canvas único continuo**; las demás son **una `SceneSection` por fase**.
- Los consumidores requieren `"urban-scenes-shared": "file:..."` y los vite configs usan
  `resolve.dedupe`, `optimizeDeps.exclude: ['urban-scenes-shared']` y `server.fs.allow: ['..']`.

## FLUJO CREAR

### Paso A — Brief (narrativa)

1. Leer `prompt-source.md` (Template Web Inmersiva).
2. Recibir el brief de la empresa (rubro, producto, datos, colores, CTA).
3. Determinar según la **guía de adaptación por industria**: objeto protagonista, estados,
   atmósferas.
4. Mapear las 11 secciones del template a **fases de la máquina** (nombres cortos:
   apertura, presentacion, interior, contexto, specs, experiencia, tecnologia, valores, datos,
   reserva, cierre) — o al arco narrativo que pida la escena.
5. **Presentar al usuario**: protagonista, fases, cámara, atmósferas. **Esperar OK antes de
   escribir código.** La narrativa debe quedar definida y acordada primero.

### Paso B — Implementación

1. `src/scenes/<nombre>/xMachine.ts` — máquina pura `derive*` con keyframes y canales.
2. `src/scenes/<nombre>/xMachine.test.ts` — tests de fases/transiciones/límites.
3. `src/scenes/<nombre>/XScene.tsx` — componente `useFrame` que anima según progreso.
4. Keyframes de cámara y atmósfera (constantes locales `CAMERA`/`ATMOSPHERE`).
5. Montaje: `SceneSection` por fase (urbancore) **o** canvas único + progreso global
   (sanabria).
6. Registro: agregar al `sceneByService`/`cameraByService` (urbancore) o al arco global
   (sanabria).
7. Si aparece infra repetida, moverla a `urban-scenes-shared` y recompilar con tsc.

### Paso C — Verificación

En el proyecto afectado (y en `urban-scenes-shared` si se tocó):

```powershell
npm run typecheck; if ($?) { npm run lint }; if ($?) { npm test }; if ($?) { npm run build }
```

## FLUJO MEJORAR

Checklist de revisión sobre la escena indicada:

- [ ] Cumple el **contrato** (máquina pura, test, `useFrame`, keyframes, infra compartida).
- [ ] **Pureza**: la máquina no importa React/tres; las matemáticas viven en `xMachine.ts`.
- [ ] **Perf**: sin allocations/meshes por frame, sin scans en loop, sin work en render.
- [ ] **Sanity cámara/fog**: keyframes coherentes, sin cortes bruscos, atmósferas por fase.
- [ ] **Mobile / reduced-motion**: degrada bien.
- [ ] **Dead code / duplicación**: sin imports huérfanos, sin `shared/` local duplicado.
- [ ] Correr la verificación obligatoria.

Al final, resumir cambios y confirmar si la escena quedó sin regresiones.

## Verificación obligatoria

Antes de dar una tarea por terminada, correr `typecheck`, `lint`, `test` y `build` en el/los
proyecto(s) afectados, y recompilar `urban-scenes-shared` (`npm run prepare` / `npm run build`)
si se modificó el paquete.

## Idioma

Responder al usuario en español; código, mensajes de commit y comentarios en inglés.
