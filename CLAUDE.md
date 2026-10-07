# Instrucciones para Claude

## Inicio obligatorio

1. Lee [ARCHITECTURE_RULES.md](ARCHITECTURE_RULES.md) antes de editar código.
2. Revisa los archivos y cambios existentes que se solapen con la tarea. El árbol puede estar sucio: no reviertas ni reformatees cambios ajenos.
3. Carga las habilidades locales necesarias desde `.claude/skills/`; combina sólo las que apliquen al alcance.
4. Define criterios de aceptación verificables antes de implementar. No declares éxito sin ejecutar las comprobaciones pertinentes.

## Contexto del proyecto

- Sistema para que usuarios puedan realizar el pago de peaje mediante la placa de su vehículo
- Vue 3 + TypeScript + Vite + TailwindCSS + Pinia + Vue Router + Axios.
- Arquitectura por dominio en `src/modules/<feature>/` con el flujo obligatorio `View → Composable → Service → API`.
- Los módulos suelen dividirse en `components/`, `views/`, `composables/`, `services/`, `mappers/`, `types/`, `constants/` y `routes/`.

## Restricciones que no se negocian

- Usa Options API con `defineComponent`, `name` PascalCase y `<script lang="ts">`; nunca `<script setup>`.
- No llames a APIs, Axios o `httpClient` desde vistas ni componentes. Las vistas coordinan composables; los composables invocan servicios; los servicios usan la capa API.
- Mantén DTOs, modelos de dominio y mappers separados. No expongas DTOs directamente a la UI.
- Toda ruta privada define `meta.permission`. Toda acción que cambia estado se protege con `v-can`; los permisos se consultan mediante `usePermissions`.
- Al crear un módulo, registra `view` y `edit` en `src/modules/auth/constants/role-permissions.ts`, y añade permiso a la ruta y al sidebar.
- Los componentes de `src/shared/ui/` solo contienen presentación: sin reglas de negocio, stores ni llamadas API.
- Estado de formulario, filtros y modales locales permanece en el componente/composable. Pinia se reserva para estado compartido o transversal.
- Las columnas, filtros y configuraciones de formularios pertenecen a `constants/`; no se definen inline en componentes.
- Datos mock: JSON adyacente al servicio y acceso tipado por servicio; nunca mocks embebidos en componentes.
- Conserva los contratos de serialización de fechas y montos. Antes de cambiar una petición o respuesta, sigue su mapper y su tipo hasta el servicio.

## Trabajo y verificación

- Prefiere cambios pequeños y acotados al módulo; no conviertas deuda técnica no relacionada durante una tarea funcional.
- Agrega o actualiza pruebas de Vitest para lógica de composables, servicios y mappers. La configuración busca pruebas en `tests/` y carga `tests/setup.ts`; crea ambos cuando introduzcas la primera prueba.
- Ejecuta `npm run build` para validar tipos y bundle. Para lint sin modificar archivos, usa `npx eslint <archivos>`; no uses `npm run lint` como verificación porque incluye `--fix`.
- Ejecuta `npm test` cuando haya pruebas relevantes. Si una comprobación no puede ejecutarse, explica el motivo y el riesgo residual.

## Selección de habilidades

- Cambio general de módulo: `vue-module`.
- Listado, creación, detalle o edición de entidad: `crud-module`; añade `crud-ui-layout` si cambia la composición de la vista.
- Peticiones HTTP, DTOs, mappers, fechas o PATCH: `api-contracts`.
- Listas, filtros, paginación, watchers, detalle reactivo o `BaseTable`: `async-data-loading`.
- Detalle alcanzable desde más de un origen o breadcrumb dinámico: `contextual-navigation`.
- Rutas, sidebar o acciones mutables: `rbac`.
- Lógica testeable nueva o modificada: `testing`.

No copies reglas extensas de `ARCHITECTURE_RULES.md` en la respuesta ni en el código: carga la habilidad y aplica sus pasos. Las reglas globales de este archivo siguen siendo obligatorias incluso si ninguna habilidad dispara.

## Herramientas locales

- `/tm:feature <módulo o cambio>`: planifica e implementa respetando la arquitectura.
- `/tm:rbac <módulo o ruta>`: audita permisos de ruta, sidebar y acciones de escritura.
- `/tm:verify [alcance]`: ejecuta la puerta de calidad no destructiva.
- `/tm:review <módulo o diff>`: revisión enfocada en contratos, arquitectura y regresiones.

## Agentes locales

- `architect`: plan de arquitectura de un cambio de dominio, sin editar.
- `vue-quality-reviewer`: revisión no mutante de Options API, capas, contratos y verificaciones.

Consulta `.claude/README.md` para el inventario y uso de plugins, habilidades y agentes del proyecto.
