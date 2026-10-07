# Vue 3 + TailwindCSS + Pinia — Reglas de Arquitectura

## Objetivo

Definir las reglas arquitectónicas, convenciones y mejores prácticas para el desarrollo de aplicaciones basadas en:

- Vue 3
- TailwindCSS
- Pinia
- TypeScript

El objetivo principal es garantizar:

- Escalabilidad
- Claridad del código
- Reutilización
- Bajo acoplamiento
- Consistencia visual y arquitectónica
- Facilidad de mantenimiento

---

# 1. Principios Fundamentales

## 1.1 Separación de Responsabilidades

La lógica de negocio NO debe vivir dentro de la UI.

Separación obligatoria:

```plaintext
View
  ↓
Composable
  ↓
Service
  ↓
API
```

---

## 1.2 Componentes Reutilizables

Los componentes reutilizables:

- NO deben conocer reglas de negocio
- NO deben acceder directamente a stores específicos
- NO deben realizar llamadas API
- Deben ser configurables mediante props
- Deben emitir eventos claros

---

## 1.3 Arquitectura por Dominio

El proyecto debe organizarse por módulos funcionales (feature-based architecture).

Evitar:

```plaintext
components/
views/
stores/
services/
```

Preferir:

```plaintext
modules/
  auth/
  users/
  payments/
  dashboard/
```

---

# 2. Estructura Oficial del Proyecto

```plaintext
src/
│
├── app/
│   ├── router/
│   ├── providers/
│   ├── plugins/
│   └── layouts/
│
├── core/
│   ├── api/
│   ├── composables/
│   ├── services/
│   ├── utils/
│   ├── constants/
│   └── types/
│
├── modules/
│   ├── auth/
│   │   ├── components/
│   │   ├── views/
│   │   ├── stores/
│   │   ├── services/
│   │   ├── composables/
│   │   ├── routes/
│   │   └── types/
│   │
│   ├── users/
│   ├── dashboard/
│   └── payments/
│
├── shared/
│   ├── ui/
│   ├── components/
│   ├── forms/
│   ├── tables/
│   ├── modals/
│   └── icons/
│
├── assets/
├── styles/
└── main.ts
```

---

# 3. Organización de Componentes

## 3.1 Componentes Base

Ubicación:

```plaintext
shared/ui/
```

Ejemplos:

```plaintext
BaseButton.vue
BaseInput.vue
BaseCard.vue
BaseModal.vue
BaseTable.vue
```

### Reglas

- Deben ser completamente reutilizables
- Deben contener únicamente lógica visual
- No deben usar stores
- No deben consumir APIs

---

## 3.2 Componentes Funcionales

Ubicación:

```plaintext
modules/<feature>/components/
```

Ejemplos:

```plaintext
PaymentSummaryCard.vue
TransactionTable.vue
RechargeModal.vue
```

### Reglas

- Pueden usar composables
- Pueden usar stores del módulo
- Pueden contener lógica específica del dominio

---

## 3.3 Definición de Componentes (Options API obligatoria)

Todos los componentes `.vue` del proyecto deben definirse con `defineComponent` y la **Options API**.

- Usar `<script lang="ts">` (NO `<script setup>`).
- Exportar como `export default defineComponent({ ... })`.
- El campo `name` es obligatorio y debe coincidir con el nombre del componente en PascalCase.
- Los stores de Pinia se instancian dentro de `data()` (`sidebar: useSidebarStore()`) y se acceden vía `this.sidebar`.
- Los iconos u otros componentes asignados a `data()` deben envolverse en `markRaw()` para evitar reactividad innecesaria.
- Si se modifica un componente existente con `<script setup>`, debe convertirse a Options API.

Estructura mínima:

```vue
<script lang="ts">
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'NombreDelComponente',
  components: { /* dependencias */ },
  props: { /* ... */ },
  emits: { /* ... */ },
  data() {
    return { /* estado local */ }
  },
  computed: { /* ... */ },
  methods: { /* ... */ },
})
</script>
```

---

# 4. Convenciones de Nombres

## Componentes

Usar PascalCase:

```plaintext
UserCard.vue
BaseButton.vue
```

---

## Stores

Formato:

```plaintext
<feature>.store.ts
```

Ejemplos:

```plaintext
auth.store.ts
payment.store.ts
theme.store.ts
```

---

## Composables

Formato obligatorio:

```plaintext
useSomething.ts
```

Ejemplos:

```plaintext
useAuth.ts
usePagination.ts
useDebounce.ts
```

---

## Services

Formato:

```plaintext
<feature>.service.ts
```

Ejemplos:

```plaintext
user.service.ts
payment.service.ts
```

---

# 5. Uso Correcto de Pinia

## 5.1 Qué debe almacenarse en un Store

Permitido:

- Usuario autenticado
- Configuración global
- Cache de datos
- Estado compartido
- Preferencias de usuario
- Estado transversal

---

## 5.2 Qué NO debe almacenarse

NO almacenar:

- Estado temporal de formularios
- Inputs locales
- Modales temporales
- Filtros locales de tablas
- Estados exclusivos de una vista

Ese estado pertenece al componente o composable.

---

# 6. Servicios y APIs

## 6.1 Prohibido llamar APIs directamente desde componentes

Incorrecto:

```ts
onMounted(async () => {
   const response = await axios.get('/users')
})
```

---

## 6.2 Patrón obligatorio

```plaintext
Service
  ↓
Composable
  ↓
Component
```

---

## 6.3 Datos mock

La data tipo mock-up **NO debe vivir embebida en componentes ni en código TypeScript**. Reglas:

- Almacenar la data en archivos `.json` ubicados junto al service que la sirve:

  ```plaintext
  modules/<feature>/services/<feature>.mock.json
  ```

- Exponerla a través de un service tipado en el mismo módulo:

  ```ts
  // modules/companies/services/companies.service.ts
  import companiesMock from './companies.mock.json'
  import type { Company } from '../types/Company'

  export function getCompanies(): Company[] {
    return companiesMock as Company[]
  }
  ```

- Las vistas (y composables) consumen el service, nunca el JSON directamente.
- Cuando se conecte el backend real, sólo cambia la implementación interna del service — vistas y composables permanecen intactos.
- Tipos del dominio (`Company`, `User`, etc.) van en `modules/<feature>/types/`.

---

## 6.4 Configuración de listas y vistas

Las definiciones declarativas de UI (columnas de tablas, filtros, formularios, etc.) **NO deben vivir embebidas en componentes**. Reglas:

- Configuraciones específicas de un módulo van en `modules/<feature>/constants/`.
- Naming: `<lista>.columns.ts`, `<form>.fields.ts`, etc. (descriptivo, no genérico).
- Configuraciones globales (menú lateral, opciones compartidas) viven en `core/constants/`.
- Los componentes solo importan las constantes; no las redefinen inline.

Ejemplo:

```ts
// modules/companies/constants/companyList.columns.ts
import type { ColumnDefinition } from '@/core/types/CommonTypes'

export const companyListColumns: ColumnDefinition[] = [
  { id: 'id', name: 'ID' },
  { id: 'name', name: 'Nombre' },
  // ...
]
```

```ts
// view
import { companyListColumns } from '../constants/companyList.columns'
```

---

# 7. TailwindCSS — Reglas Oficiales

## 7.1 Evitar clases repetidas

Incorrecto:

```html
<div class="bg-white rounded-lg shadow-md p-4 border border-gray-200">
```

repetido en múltiples vistas.

---

## 7.2 Extraer patrones visuales

Opciones válidas:

### Componentes UI

```vue
<BaseCard></BaseCard>
```

### Clases reutilizables

```css
.card-container {
    @apply bg-white rounded-xl shadow-sm border border-gray-200 p-4;
}
```

---

# 8. Layouts

Todos los layouts deben estar centralizados:

```plaintext
app/layouts/
```

Ejemplos:

```plaintext
AuthLayout.vue
DashboardLayout.vue
EmptyLayout.vue
```

---

# 9. TypeScript

TypeScript es obligatorio en:

- stores
- APIs
- DTOs
- formularios
- modelos
- tablas

---

# 10. Testing

Herramientas recomendadas:

- Vitest
- Vue Test Utils
- Playwright

---

# 11. Convenciones para Features CRUD (Listado / Detalle / Creación / Edición)

Cuando un módulo (`modules/<feature>/`) incluye operaciones CRUD sobre una entidad — listado, detalle, creación y/o edición — se debe seguir la convención utilizada en los módulos de referencia (`companies`, `employees`). 
Esto garantiza coherencia entre features, predictibilidad en la navegación y archivos consistentes entre módulos.

## 11.1 Plural vs. singular

- La carpeta del módulo va en **plural**: `modules/companies/`, `modules/employees/`.
- El nombre de la entidad de dominio (usado en views, types, composables y componentes) va en **singular**: `Company`, `Employee`.
- Los servicios, al representar el conjunto/colección, van en **plural**: `companies.service.ts`.

---

## 11.2 Vistas (`modules/<feature>/views/`)

Para cada feature CRUD se crean las siguientes vistas, todas en **PascalCase + singular** con el sufijo `View`:

| Propósito                       | Archivo                       |
|---------------------------------|-------------------------------|
| Listado                         | `<Entity>ListView.vue`        |
| Creación                        | `<Entity>CreateView.vue`      |
| Detalle (incluye edición inline)| `<Entity>DetailsView.vue`     |

Ejemplos:

```plaintext
modules/companies/views/CompanyListView.vue
modules/companies/views/CompanyCreateView.vue
modules/companies/views/CompanyDetailsView.vue
```

> **Nota sobre edición:** por convención el modo edición vive **dentro** del `DetailsView`, alternado mediante un composable `use<Entity>Edit`. 
**No** se crea un `EditView` independiente salvo que el flujo de edición sea sustancialmente distinto al de detalle.

> **Excepción — creación contextual:** cuando la creación de la entidad ocurre **siempre en el contexto de otra entidad **no se crea `<Entity>CreateView.vue`** y la ruta `<feature>-create` se omite. El service mantiene expuesta la operación `create<Entity>` para que la consuma el contexto contenedor, y el motivo debe documentarse en `routes/index.ts` para que un lector futuro entienda la ausencia:
>
> ```ts
> /**
>  * NOTA: la creación de terminales se realiza únicamente desde el contexto
>  * de una plaza (módulo `sites`), por lo que aquí no se define una ruta
>  * `terminals-create`.
>  */
> export const terminalsRoutes: RouteRecordRaw[] = [
>   { path: 'terminals', name: 'terminals', ... },
>   { path: 'terminals/:id', name: 'terminals-detail', ... },
>   // sin 'terminals-create'
> ]
> ```

> **Vistas por tabs (detalle con `keep-alive`):** cuando un detalle se organiza
> en tabs y cada tab se mantiene vivo con `<keep-alive>`, **al cambiar de tab se
> cancela cualquier acción de edición o creación en curso**, volviendo al modo
> lectura/listado. Se implementa con el hook `deactivated()` del tab (que se
> dispara al desactivarse bajo `keep-alive`), llamando al `cancelEdit()` del
> composable de edición y/o restableciendo el modo de creación:
>
> ```ts
> deactivated(): void {
>   if (this.mode === 'create') this.mode = 'list'
>   if (this.editing) this.cancelEdit()
> },
> ```
>
> `keep-alive` sigue preservando los datos ya cargados (listados, detalle), pero
> **no** los borradores: un borrador a medio editar se descarta al salir del tab
> para evitar estados ambiguos entre pestañas.

---

## 11.3 Rutas (`modules/<feature>/routes/index.ts`)

Las rutas siguen el patrón `<feature>` / `<feature>-create` / `<feature>-detail`, donde `<feature>` coincide con el nombre del módulo en plural:

```ts
import type { RouteRecordRaw } from 'vue-router'

export const <feature>Routes: RouteRecordRaw[] = [
  {
    path: '<feature>',
    name: '<feature>',
    component: () => import('@/modules/<feature>/views/<Entity>ListView.vue'),
    meta: { title: '...' },
  },
  {
    path: '<feature>/create',
    name: '<feature>-create',
    component: () => import('@/modules/<feature>/views/<Entity>CreateView.vue'),
    meta: { title: 'Nuevo ...' },
  },
  {
    path: '<feature>/:id',
    name: '<feature>-detail',
    component: () => import('@/modules/<feature>/views/<Entity>DetailsView.vue'),
    meta: { title: 'Detalle de ...' },
  },
]
```

---

## 11.4 Composables (`modules/<feature>/composables/`)

Se separa la lógica por flujo — un composable por responsabilidad, todos en singular:

| Flujo                         | Composable                |
|-------------------------------|---------------------------|
| Crear / formulario base       | `use<Entity>Form.ts`      |
| Edición inline desde detalle  | `use<Entity>Edit.ts`      |

Ejemplos:

```plaintext
useCompanyForm.ts
useCompanyEdit.ts
useEmployeeForm.ts
useEmployeeEdit.ts
```

Las vistas no contienen lógica de fetching/mutación: importan el composable correspondiente y exponen su API mediante `setup()`.

### 11.4.1 Origen de las funciones consumidas por el composable

Los composables importan según responsabilidad — del **service** las operaciones de red y validación, del **mapper** los helpers de forma:

| Función                                          | Origen                                       |
|--------------------------------------------------|----------------------------------------------|
| `create<Entity>`, `update<Entity>`               | `services/<feature>.service.ts`              |
| `validate<Entity>Form`, `validate<Entity>Update` | `services/<feature>.service.ts`              |
| `buildEmpty<Entity>Form`                         | `mappers/<entity>Form.mapper.ts`             |
| `build<Entity>UpdateDto`                         | `mappers/<entity>Update.mapper.ts`           |

Razones:

- Los `buildEmpty*` y `build*UpdateDto` son **funciones puras** sobre modelos de vista/DTO — pertenecen al mapper, no al service (que está acoplado a la red).
- Mantiene el principio "el service hace HTTP + validación; los mappers transforman estructuras".

Ejemplo (`useCompanyForm.ts`):

```ts
import { createCompany, validateCompanyForm } from '../services/companies.service'
import { buildEmptyCompanyForm } from '../mappers/companyForm.mapper'
```

---

## 11.5 Servicios (`modules/<feature>/services/`)

El service se nombra con la entidad en **plural** y debe exponer todas las operaciones del CRUD de manera tipada:

```plaintext
companies.service.ts   →  getCompanies, getCompanyDetails, createCompany, updateCompany, ...
employees.service.ts   →  getEmployees, getEmployeeDetails, createEmployee, updateEmployee, ...
```

Vistas y composables consumen exclusivamente el service — nunca el cliente HTTP ni los archivos `.mock.json` directamente.

El service también es la **única** capa que conoce los DTOs del backend: recibe DTOs del cliente HTTP, los pasa por los mappers y expone únicamente modelos de vista (camelCase) a sus consumidores. Ver §11.6 (DTOs y modelos de vista) y §11.7 (Mappers).

### 11.5.1 Signatures: el service habla en modelo de vista, no en DTO

Las funciones públicas del service reciben y devuelven **modelos de vista** (camelCase). El service aplica internamente el mapper correspondiente para construir/parsear el DTO. Las vistas y composables nunca ven el DTO.

| Operación              | Signature obligatoria                                                  |
|------------------------|-------------------------------------------------------------------------|
| Listado                | `get<Entities>(query?): Promise<PaginatedResponse<<Entity>>>`           |
| Detalle                | `get<Entity>Details(id): Promise<<Entity>Details \| null>`              |
| Creación               | `create<Entity>(form: <Entity>Form): Promise<Create<Entity>Result>`     |
| Actualización          | `update<Entity>(id, payload: Update<Entity>PayloadDto): Promise<<Entity>Details>` |
| Validación creación    | `validate<Entity>Form(form: <Entity>Form): <Entity>FormErrors`          |
| Validación edición     | `validate<Entity>Update(draft: <Entity>Details): <Entity>UpdateErrors`  |

Reglas:

- `create<Entity>` recibe el **modelo de vista** (`<Entity>Form`) y aplica `map<Entity>FormToDto` internamente antes del POST. El llamador (composable) no construye el DTO.
- `update<Entity>` es el único caso donde el service recibe directamente un DTO (`Update<Entity>PayloadDto`), porque el DTO ya viene armado por `build<Entity>UpdateDto(draft, original)` del mapper de edición — el composable de edición consume ese mapper y pasa el resultado al service.
- Las funciones de validación son **puras** (no tocan red, no acceden a stores) y operan sobre modelos de vista, devolviendo `<Entity>FormErrors` / `<Entity>UpdateErrors` con claves camelCase.
- El retorno de creación es un objeto view-side (`Create<Entity>Result = { id: string }`) — nunca el DTO de respuesta (`Create<Entity>ResponseDto`), que vive sólo en el archivo de tipos del DTO.

Referencia de implementación: `companies.service.ts` (signatures completas para el CRUD de compañías).

### 11.5.2 Payloads de actualización (PATCH): sólo campos modificados

En los formularios de actualización (PATCH) **el payload sólo debe contener los campos cuyo valor difiere del original**. No se envían campos sin cambios, ni siquiera con el mismo valor.

Razones:

- Refleja la semántica real de PATCH (sub-recurso parcial, no reemplazo total).
- Reduce el riesgo de pisar campos que otro usuario haya modificado en paralelo.
- Acota el `requestBody` del backend y simplifica auditorías ("qué cambió" se lee directo del payload).
- Evita disparar validaciones/efectos colaterales por campos que el usuario no tocó.

Patrón obligatorio:

1. El composable de edición (`use<Entity>Edit.ts`) guarda **dos copias** al entrar en modo edición — `draft` (mutable, ligado al formulario) y `original` (snapshot inmutable).
2. El mapper `build<Entity>UpdateDto(draft, original)` (en `modules/<feature>/mappers/<entity>Update.mapper.ts`) recorre **únicamente** los paths declarados en `<entity>Editable.fields.ts` y agrega al DTO sólo aquellos donde `getValueAtPath(draft, path) !== getValueAtPath(original, path)`.
3. Si el DTO resultante está vacío, el composable **no llama al backend** — sale del modo edición devolviendo el `original` como éxito.

Referencia de implementación: `buildCompanyUpdateDto` en `mappers/companyUpdate.mapper.ts` del módulo `companies`, consumido por `useCompanyEdit.ts`.

---

## 11.6 Types (`modules/<feature>/types/`)

Cada entidad CRUD se modela en **dos niveles**, separados por intención:

| Capa             | Convención  | Naming                                                                                  | Uso                                                                                  |
|------------------|-------------|-----------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| DTO (API)        | snake_case  | `<Entity>Dto.ts`, `<Entity>DetailsDto.ts`, `<Entity>FormDto.ts`, `<Entity>UpdateDto.ts` | Refleja el contrato exacto del backend. Sólo lo manipulan el `service` y los mappers. |
| Modelo de vista  | camelCase   | `<Entity>.ts`, `<Entity>Details.ts`, `<Entity>Form.ts`                                   | Lo que consumen composables, plantillas, validadores y constants.                    |

Reglas:

- Las vistas y composables **nunca** importan tipos `*Dto`. Sólo el service y los mappers conocen la forma DTO.
- Los validadores trabajan sobre el modelo de vista. Las claves de error usan dot-notation **camelCase** (`legalRepresentative.firstName`, `bankDetails.accountNumber`).
- Cuando un campo es parte tanto del detalle como del formulario (p. ej. `bankDetails`), reutilizar el tipo del view model (no redefinir).
- `<Entity>Update.ts` (view-side) sólo contiene el tipo de errores de edición (`<Entity>UpdateErrors`). El tipo de payload PATCH vive en `<Entity>UpdateDto.ts`.
- Los DTOs nunca exponen tipos sin el sufijo `Dto`. Si un sub-objeto se reutiliza (p. ej. representante, datos bancarios) también termina en `…Dto` (`CompanyDetailsLegalRepresentativeDto`, `CompanyBankDetailDto`).
- **Sub-DTOs compartidos entre services** (cuando una misma forma cruda viaja en endpoints distintos — p. ej. el snapshot del sujeto en lista negra, que aparece tanto en `/entries/` como en `/movements/`) viven en **su propio archivo** dentro de `types/`, sin entidad padre en el nombre:

  ```plaintext
  modules/blacklist/types/
  ├── BlacklistEntryDto.ts          ← usa BlacklistSnapshotDto
  ├── BlacklistMovementDto.ts       ← usa BlacklistSnapshotDto
  └── BlacklistSnapshotDto.ts       ← sub-DTO compartido
  ```

  El mapper del sub-DTO también se extrae a su propio archivo (`blacklistSnapshot.mapper.ts`) y se invoca desde los mappers principales. Evita duplicación entre services y mantiene una sola fuente de verdad del shape compartido.

Para una entidad CRUD se crean, como mínimo:

| Vista (camelCase)        | DTO (snake_case)            | Uso                                                         |
|--------------------------|-----------------------------|-------------------------------------------------------------|
| `<Entity>.ts`            | `<Entity>Dto.ts`            | Listado                                                     |
| `<Entity>Details.ts`     | `<Entity>DetailsDto.ts`     | Detalle                                                     |
| `<Entity>Form.ts`        | `<Entity>FormDto.ts`        | Formulario de creación + DTO `CreateCompanyPayloadDto`      |
| `<Entity>Update.ts`      | `<Entity>UpdateDto.ts`      | Errores de edición + DTO PATCH parcial                      |

Ejemplo (`companies`):

```plaintext
modules/companies/types/
├── Company.ts                  ← view model lista
├── CompanyDto.ts               ← DTO lista
├── CompanyDetails.ts           ← view model detalle
├── CompanyDetailsDto.ts        ← DTO detalle
├── CompanyForm.ts              ← view model formulario creación
├── CompanyFormDto.ts           ← DTO creación (request/response)
├── CompanyUpdate.ts            ← tipos de errores de edición (camelCase)
└── CompanyUpdateDto.ts         ← DTO PATCH parcial
```

---

## 11.7 Mappers (`modules/<feature>/mappers/`)

Toda traducción DTO ↔ modelo de vista vive en `modules/<feature>/mappers/`. **Un archivo por flujo**, naming descriptivo:

| Flujo            | Archivo                          | Funciones exportadas                                              |
|------------------|----------------------------------|-------------------------------------------------------------------|
| Listado          | `<entity>.mapper.ts`             | `map<Entity>DtoToView`                                            |
| Detalle          | `<entity>Details.mapper.ts`      | `map<Entity>DetailsDtoToView`                                     |
| Creación         | `<entity>Form.mapper.ts`         | `map<Entity>FormToDto`, **`buildEmpty<Entity>Form`**              |
| Edición parcial  | `<entity>Update.mapper.ts`       | `build<Entity>UpdateDto(draft, original)`                         |

Reglas:

- Los mappers son **funciones puras**: sin side effects, sin acceso a stores, sin HTTP, sin imports de Vue.
- El service es la **única** capa que invoca mappers. Composables y vistas siempre reciben/entregan modelos de vista.
- `buildEmpty<Entity>Form()` (el constructor del formulario vacío) vive **en el form mapper**, no en el service. Razón: es una función pura sobre el modelo de vista; el composable la importa directamente del mapper (ver §11.4.1).
- El mapper de edición parcial **es** el responsable de traducir paths del view model (camelCase) a paths del DTO (snake_case). La tabla de traducción (`VIEW_TO_DTO_PATH`) vive **en el propio archivo del mapper**, no en `constants/`.
- Los paths declarados en `<entity>Editable.fields.ts` se expresan **siempre en camelCase** (sobre el view model). El mapper hace la traducción al enviar.

Patrón del mapper de edición parcial:

```ts
// modules/companies/mappers/companyUpdate.mapper.ts
const VIEW_TO_DTO_PATH: Record<CompanyEditableField, string> = {
  name: 'name',
  companyType: 'company_type',
  'bankDetails.bankCode': 'bank_details.bank_code',
  'legalRepresentative.firstName': 'legal_representative.first_name',
  // ...
}

export function buildCompanyUpdateDto(
  draft: CompanyDetails,
  original: CompanyDetails,
): UpdateCompanyPayloadDto {
  const dto: Record<string, unknown> = {}
  for (const field of editableCompanyFields) {
    const next = getValueAtPath(draft, field)
    const prev = getValueAtPath(original, field)
    if (next !== prev) {
      setValueAtPath(dto, VIEW_TO_DTO_PATH[field], next)
    }
  }
  return dto as UpdateCompanyPayloadDto
}
```

Referencia de implementación: `modules/companies/mappers/` (mapper completo para listado, detalle, creación y edición).

---

## 11.8 Constants (`modules/<feature>/constants/`)

Las configuraciones declarativas se separan por uso, **una por archivo**, con naming descriptivo en singular:

| Configuración                                  | Archivo                       |
|------------------------------------------------|-------------------------------|
| Columnas del listado                           | `<entity>List.columns.ts`     |
| Opciones (selects, radios) del formulario      | `<entity>Form.options.ts`     |
| Etiquetas / mappers para la vista de detalle   | `<entity>Detail.labels.ts`    |
| Definición de campos editables inline          | `<entity>Editable.fields.ts`  |

Ejemplos:

```plaintext
modules/companies/constants/companyList.columns.ts
modules/companies/constants/companyForm.options.ts
modules/companies/constants/companyDetail.labels.ts
modules/companies/constants/companyEditable.fields.ts
```

---

## 11.9 Layout estándar de las vistas

Las tres vistas CRUD comparten una estructura de layout fija. Respetarla evita inconsistencias visuales entre módulos y permite que cualquier vista nueva sea predecible para el equipo.

### 11.9.1 Elementos comunes a las tres vistas

- **`<PageHeader>`** siempre como primer elemento, con `title`, `description` y un slot `#actions` para los botones contextuales de la vista.
- **`<BaseCard>`** como contenedor estándar de bloques de contenido. No se usan `<div class="bg-white rounded-lg shadow ...">` ad-hoc.
- Los iconos provienen de `lucide-vue-next` (`Plus`, `ArrowLeft`, `Pencil`, …) y se registran en `components`.
- Espaciado vertical entre secciones del cuerpo: `space-y-12`.

---

### 11.9.2 `ListView` — layout

Estructura fija:

```vue
<template>
  <PageHeader title="..." description="...">
    <template #actions>
      <BaseButton @click="onCreate">
        <Plus class="h-4 w-4" />
        Añadir <entidad>
      </BaseButton>
    </template>
  </PageHeader>

  <BaseCard :padded="false">
    <BaseTable
      v-model:column-filters="columnFilters"
      v-model:selected-columns="selectedColumns"
      :fields-by-id="fieldsById"
      :first-column-is-link="true"
      :hide-create-button="true"
      :hide-update-button="true"
      :list-fields="columns"
      :loading-list-status="LOADING_DATA_STATUS.DataLoaded"
      :src-data="tableItems"
      margin-top-table="mt-0"
      table-padding="px-4 py-4 sm:px-6 lg:px-8"
      @viewItemDetails="onViewItemDetails"
    />
  </BaseCard>
</template>
```

Reglas:

- Único botón de acción en `#actions`: **Añadir &lt;entidad&gt;** que navega a `<feature>-create`.
- `BaseCard` con `:padded="false"` porque el padding lo aplica la propia tabla.
- Las columnas vienen de `<entity>List.columns.ts` (nunca inline).
- La navegación al detalle se resuelve en el handler `onViewItemDetails` haciendo `$router.push({ name: '<feature>-detail', params: { id } })`.

---

### 11.9.3 `CreateView` — layout

Estructura fija:

```vue
<template>
  <PageHeader title="Nueva <entidad>" description="...">
    <template #actions>
      <BaseButton variant="ghost" :disabled="submitting" @click="onCancel">
        <ArrowLeft class="h-4 w-4" />
        Volver
      </BaseButton>
    </template>
  </PageHeader>

  <form class="space-y-12" novalidate @submit.prevent="onSubmit">
    <!-- Una <section> por bloque temático -->
    <section
      class="grid grid-cols-1 gap-6 border-b border-slate-200 pb-12 lg:grid-cols-3 lg:gap-10 dark:border-slate-800"
    >
      <header class="space-y-3 lg:col-span-1">
        <h2 class="text-base font-semibold text-slate-900 dark:text-white">
          Título del bloque
        </h2>
        <p class="text-sm text-slate-500 dark:text-slate-400">
          Descripción breve del bloque.
        </p>
        <!-- Solo en el primer bloque: leyenda de campos obligatorios -->
        <p
          class="rounded-md bg-amber-50 px-3 py-2 text-sm font-semibold text-red-600 dark:bg-amber-500/10 dark:text-red-400"
        >
          [*] Campos obligatorios.
        </p>
      </header>

      <BaseCard class="lg:col-span-2">
        <div class="grid grid-cols-1 gap-4 md:grid-cols-4">
          <!-- BaseInput / BaseSelect / BaseTextarea / BaseCheckbox -->
        </div>
      </BaseCard>
    </section>

    <!-- ... más secciones ... -->

    <!-- Última sección SIN border-b -->
    <section class="grid grid-cols-1 gap-6 lg:grid-cols-3 lg:gap-10">
      <!-- ... -->
    </section>

    <!-- Barra de acciones inferior -->
    <div
      class="flex flex-col-reverse items-stretch gap-2 sm:flex-row sm:items-center sm:justify-end"
    >
      <p v-if="hasErrors" class="mr-auto text-sm text-red-600 dark:text-red-400">
        Hay campos por completar. Revisa los apartados marcados.
      </p>
      <p v-if="submitError" class="mr-auto text-sm text-red-600 dark:text-red-400">
        {{ submitError }}
      </p>
      <BaseButton type="button" variant="secondary" :disabled="submitting" @click="onCancel">
        Cancelar
      </BaseButton>
      <BaseButton type="submit" :disabled="submitting">
        {{ submitting ? 'Guardando…' : 'Crear <entidad>' }}
      </BaseButton>
    </div>
  </form>
</template>
```

Reglas:

- Un único `<form novalidate @submit.prevent="onSubmit">` envolviendo todas las secciones, con `space-y-12`.
- Cada bloque temático es una `<section>` con layout **2 columnas en desktop** (`lg:grid-cols-3`):
  - **Izquierda (`lg:col-span-1`)**: `<header>` con `<h2>` (título del bloque) y `<p>` (descripción breve).
  - **Derecha (`lg:col-span-2`)**: `<BaseCard>` con los campos en una grilla interna (`grid grid-cols-1 gap-4 md:grid-cols-2|md:grid-cols-4`).
- Todas las secciones excepto la última llevan `border-b border-slate-200 pb-12 dark:border-slate-800`.
- Leyenda **`[*] Campos obligatorios`** únicamente en el `<header>` de la primera sección.
- Acción única en el header: **Volver** (`variant="ghost"` + icono `ArrowLeft`).
- Acciones del formulario al final, fuera de cualquier `<section>`: **Cancelar** + **Crear &lt;entidad&gt;**, alineadas a la derecha (`sm:justify-end`), con mensaje de error a la izquierda (`mr-auto`).
- Las opciones de selects vienen de `<entity>Form.options.ts`.

---

### 11.9.4 `DetailsView` — layout

Reutiliza la misma cuadrícula que `CreateView` (header izquierda + `BaseCard` derecha por sección), pero alterna entre **modo lectura** y **modo edición inline** controlado por el composable `use<Entity>Edit`.

Estructura fija:

```vue
<template>
  <PageHeader :title="headerTitle" :description="headerDescription">
    <template #actions>
      <template v-if="!editing">
        <BaseBadge v-if="entity" :tone="entity.is_active ? 'success' : 'danger'">
          {{ entity.is_active ? 'Activa' : 'Inactiva' }}
        </BaseBadge>
        <BaseButton variant="ghost" @click="onBack">
          <ArrowLeft class="h-4 w-4" />
          Volver
        </BaseButton>
        <BaseButton v-if="entity" @click="onEdit">
          <Pencil class="h-4 w-4" />
          Editar
        </BaseButton>
      </template>
      <template v-else>
        <BaseButton variant="secondary" :disabled="saving" @click="onCancel">
          Cancelar
        </BaseButton>
        <BaseButton :disabled="saving" @click="onSave">
          {{ saving ? 'Guardando…' : 'Guardar cambios' }}
        </BaseButton>
      </template>
    </template>
  </PageHeader>

  <!-- Estados de carga -->
  <div v-if="isLoading" class="rounded-md bg-slate-50 px-4 py-6 text-center text-sm text-slate-500 dark:bg-slate-900/40 dark:text-slate-400">
    Cargando detalle…
  </div>
  <div v-else-if="isErrorState" class="rounded-md bg-red-50 px-4 py-3 text-sm font-medium text-red-700 dark:bg-red-500/10 dark:text-red-300">
    {{ loadError }}
  </div>
  <div v-else-if="isLoaded && !entity" class="rounded-md bg-amber-50 px-4 py-3 text-sm font-medium text-amber-800 dark:bg-amber-500/10 dark:text-amber-300">
    No se encontró el registro solicitado.
  </div>

  <div v-else-if="entity" class="space-y-12">
    <!-- Una <section> por bloque (misma cuadrícula que CreateView) -->
    <section class="grid grid-cols-1 gap-6 border-b border-slate-200 pb-12 lg:grid-cols-3 lg:gap-10 dark:border-slate-800">
      <header class="space-y-3 lg:col-span-1">
        <h2 class="text-base font-semibold text-slate-900 dark:text-white">Bloque</h2>
        <p class="text-sm text-slate-500 dark:text-slate-400">Descripción breve.</p>
        <!-- Solo cuando editing=true: leyenda de obligatorios en el primer bloque -->
        <p v-if="editing" class="rounded-md bg-amber-50 px-3 py-2 text-sm font-semibold text-red-600 dark:bg-amber-500/10 dark:text-red-400">
          [*] Campos obligatorios.
        </p>
      </header>

      <BaseCard class="lg:col-span-2">
        <dl class="grid grid-cols-1 gap-x-8 gap-y-5 md:grid-cols-2">
          <!-- Campo editable: input cuando canEdit, DetailField si no -->
          <BaseInput
            v-if="canEdit('name') && draft"
            v-model="draft.name"
            label="Nombre"
            required
            :error="errors.name"
          />
          <DetailField v-else label="Nombre" :value="entity.name" />

          <!-- Campo no editable: siempre DetailField -->
          <DetailField label="Código" :value="entity.code" />
        </dl>
      </BaseCard>
    </section>

    <!-- Última sección "Auditoría" SIEMPRE en lectura, sin border-b -->
    <section class="grid grid-cols-1 gap-6 lg:grid-cols-3 lg:gap-10">
      <header class="space-y-3 lg:col-span-1">
        <h2 class="text-base font-semibold text-slate-900 dark:text-white">Auditoría</h2>
        <p class="text-sm text-slate-500 dark:text-slate-400">
          Trazabilidad de creación y última modificación.
        </p>
      </header>
      <BaseCard class="lg:col-span-2">
        <dl class="grid grid-cols-1 gap-x-8 gap-y-5 md:grid-cols-2">
          <DetailField label="Creado por" :value="entity.created_by" />
          <DetailField label="Creado el" :value="entity.created_on" />
          <DetailField label="Actualizad por" :value="entity.updated_by" />
          <DetailField label="Actualizado el" :value="entity.updated_on" />
        </dl>
      </BaseCard>
    </section>

    <!-- Barra de acciones inferior — solo cuando editing -->
    <div
      v-if="editing"
      class="flex flex-col-reverse items-stretch gap-2 border-t border-slate-200 pt-6 sm:flex-row sm:items-center sm:justify-end dark:border-slate-800"
    >
      <p v-if="hasErrors" class="mr-auto text-sm text-red-600 dark:text-red-400">
        Hay campos por completar. Revisa los apartados marcados.
      </p>
      <p v-if="saveError" class="mr-auto text-sm text-red-600 dark:text-red-400">
        {{ saveError }}
      </p>
      <BaseButton variant="secondary" :disabled="saving" @click="onCancel">
        Cancelar
      </BaseButton>
      <BaseButton :disabled="saving" @click="onSave">
        {{ saving ? 'Guardando…' : 'Guardar cambios' }}
      </BaseButton>
    </div>
  </div>
</template>
```

Reglas:

- **Acciones del header son contextuales** según `editing`:
  - Lectura: `BaseBadge` de estado (si aplica) + **Volver** (`ghost`) + **Editar** (`Pencil`).
  - Edición: **Cancelar** (`secondary`) + **Guardar cambios`**.
- Antes del cuerpo, **tres bloques de estado** mutuamente excluyentes (loading / error / empty) con el styling estándar (`rounded-md` + colores temáticos slate / red / amber).
- El cuerpo (`<div v-else-if="entity" class="space-y-12">`) reutiliza **la misma cuadrícula que `CreateView`** — secciones con header a la izquierda y `BaseCard` a la derecha, separadas por `border-b` excepto la última.
- Dentro de cada `BaseCard` se usa `<dl class="grid grid-cols-1 gap-x-8 gap-y-5 md:grid-cols-2">` y por cada campo:
  - Si es editable y `editing`: `<BaseInput|BaseSelect|BaseTextarea|BaseCheckbox>` enlazado al `draft`.
  - Si no: `<DetailField label="..." :value="..." />`.
  - El switch se resuelve con `canEdit(path)` que combina `editing` con `isEditable<Entity>Field(path)` (de `<entity>Editable.fields.ts`).
- La última sección es siempre **Auditoría** en modo lectura (`id`, `created_on`, `updated_on`, etc.) y NO tiene `border-b`.
- Barra de acciones inferior **solo visible en modo edición**, con `border-t` y los mismos botones que el header de edición — para evitar tener que volver arriba en formularios largos.

---

## 11.10 Resumen visual de un módulo CRUD

```plaintext
modules/<feature>/                       ← plural
├── views/
│   ├── <Entity>ListView.vue             ← singular + sufijo View
│   ├── <Entity>CreateView.vue
│   └── <Entity>DetailsView.vue          ← incluye modo edición inline
├── routes/
│   └── index.ts                         ← <feature>, <feature>-create, <feature>-detail
├── composables/
│   ├── use<Entity>Form.ts
│   └── use<Entity>Edit.ts
├── services/
│   └── <feature>.service.ts             ← plural; única capa que conoce DTOs
├── mappers/                             ← DTO ↔ view model (funciones puras)
│   ├── <entity>.mapper.ts
│   ├── <entity>Details.mapper.ts
│   ├── <entity>Form.mapper.ts
│   └── <entity>Update.mapper.ts
├── types/
│   ├── <Entity>.ts                      ← view model (camelCase)
│   ├── <Entity>Dto.ts                   ← DTO API (snake_case)
│   ├── <Entity>Details.ts
│   ├── <Entity>DetailsDto.ts
│   ├── <Entity>Form.ts
│   ├── <Entity>FormDto.ts
│   ├── <Entity>Update.ts                ← sólo errores de edición
│   └── <Entity>UpdateDto.ts             ← DTO PATCH parcial
└── constants/
    ├── <entity>List.columns.ts
    ├── <entity>Form.options.ts
    ├── <entity>Detail.labels.ts
    └── <entity>Editable.fields.ts       ← paths en camelCase (view model)
```

---

## 11.11 Sub-flujos no-CRUD (acciones puntuales sobre una entidad)

Cuando un módulo expone una **acción puntual** que no es Listado/Detalle/Creación/Edición — p. ej. desincorporación de una entrada (`release`), inclusión de un dispositivo en lista negra, carga masiva multipart (`batch upload`) — la operación sigue **el mismo patrón Form/DTO/mapper** que las operaciones CRUD, sólo que sin entidad CRUD propia.

Estructura obligatoria por sub-flujo:

| Pieza                 | Archivo                                  | Contenido                                           |
|-----------------------|------------------------------------------|-----------------------------------------------------|
| Modelo de vista       | `types/<Action>.ts`                      | `<Action>Form` (camelCase) + `<Action>Errors`       |
| DTO                   | `types/<Action>Dto.ts`                   | `<Action>PayloadDto` (snake_case)                   |
| Mapper                | `mappers/<action>.mapper.ts`             | `map<Action>FormToDto`                              |
| Validación            | `services/<feature>.service.ts`          | `validate<Action>(form: <Action>Form)`              |
| Llamada de red        | `services/<feature>.service.ts`          | recibe `<Action>Form`, aplica el mapper internamente |

Reglas:

- La validación y el mapeo son **puros** (sin red, sin stores, sin Vue) — igual que en los flujos CRUD.
- Las claves de error y los campos del formulario van en **camelCase** sobre el modelo de vista.
- El service nunca expone el DTO al composable / vista; lo arma internamente.
- Para operaciones multipart (file upload), el mapper produce un DTO con `file: File | null`; el service construye el `FormData` a partir del DTO.

Ejemplos en el codebase:

| Sub-flujo                     | Módulo      | Archivos                                                             |
|-------------------------------|-------------|----------------------------------------------------------------------|
| Desincorporación de blacklist | `blacklist` | `BlacklistRelease.ts` + `BlacklistReleaseDto.ts` + `blacklistRelease.mapper.ts` + `validateBlacklistRelease`, `releaseBlacklistEntry` |
| Inclusión en blacklist        | `devices`   | `DeviceBlacklist.ts` + `DeviceBlacklistDto.ts` + `deviceBlacklist.mapper.ts` + `validateDeviceBlacklist`, `addDeviceToBlacklist` |
| Carga masiva (multipart)      | `devices`   | `BatchUploadForm.ts` + `BatchUploadFormDto.ts` + `batchUploadForm.mapper.ts` + `validateBatchUploadForm`, `createBatchUpload` |

---

## 11.12 Watcher de parámetros de ruta en `DetailsView`

Las vistas de detalle (`<Entity>DetailsView`) dependen de `$route.params.id` para cargar la entidad. El watcher debe cumplir tres requisitos:

1. **`immediate: true`** — para disparar la carga inicial sin depender de `mounted()`.
2. **Guardia de valor vacío** — cuando el usuario navega fuera, el param puede volverse `undefined`; la carga no debe dispararse.
3. **Cancelación de edición** — si la vista soporta edición inline, cancelar el borrador al cambiar de entidad.

### Patrón canónico

```ts
computed: {
  routeId(): string {
    return String(this.$route.params.id ?? '')
  },
},

watch: {
  routeId: {
    immediate: true,
    handler(value: string) {
      if (this.editing) this.cancelEdit() // solo si la vista soporta edición
      if (value) this.loadEntity()
    },
  },
},
```

- La computed `routeId` centraliza la extracción del parámetro y la convierte a `string`. Todos los métodos de carga deben usar `this.routeId` en lugar de `this.$route.params.id`.
- No se necesita `mounted()` / `created()` para la carga de la entidad — el watcher con `immediate` la reemplaza.

> **Anti-patrón:** declarar `mounted() { this.loadEntity() }` junto con un watcher sin `immediate`. Esto duplica la carga cuando ambos se disparan y no protege contra navegaciones rápidas (race condition).

---

## 11.13 `BaseTable` — contrato de carga inicial

`BaseTable` (`shared/components/table/BaseTable.vue`) **no dispara la carga inicial automáticamente**. La carga inicial es responsabilidad exclusiva del componente padre, en su propio `mounted()`.

Reglas:

- El componente padre que usa `BaseTable` debe llamar a su `loadXxx(1)` (o equivalente) en `mounted()`.
- El evento `@reloadData` de `BaseTable` se reserva para **recargas explícitas** disparadas por el usuario (botón "Actualizar", confirmación de filtros, etc.) — nunca para la carga inicial.
- `BaseTable` **no debe** declarar un `mounted()` que emita `reloadData` por su cuenta — eso produce doble llamada al endpoint (una desde el `mounted()` del padre, otra desde el `$nextTick` del hijo).

Patrón obligatorio en el padre:

```ts
export default defineComponent({
  // ...
  data() {
    return {
      items: [] as <Entity>[],
      pagination: null as PaginationData | null,
      currentPage: 1,
      loadingStatus: LOADING_DATA_STATUS.DataNotLoaded as number,
    }
  },

  mounted() {
    this.load(1)                       // ← carga inicial: SÓLO aquí
  },

  methods: {
    async load(page: number): Promise<void> { /* ... */ },
    onReload(): void {
      this.load(this.currentPage)      // ← recargas explícitas vía @reloadData
    },
    onPageChange(page: number): void {
      this.load(page)
    },
  },
})
```

```vue
<BaseTable
  :loading-list-status="loadingStatus"
  :src-data="tableItems"
  :pagination="pagination"
  @reloadData="onReload"
  @update:page="onPageChange"
/>
```

> **Anti-patrón:** declarar un `mounted()` en `BaseTable` que evalúe `if (neverLoadedList) $nextTick(() => emit('reloadData'))`. Resulta en dos llamadas al endpoint: la primera desde el `mounted()` del padre (que mete a `loadingStatus = DataLoading` síncronamente), y la segunda en el siguiente tick desde el `reloadData` autoemitido por `BaseTable` (que evaluó `neverLoadedList = true` antes de que el padre cambiara el estado, porque los hijos montan antes que el padre).

---

## 11.14 `loadToken` — guard contra race conditions en cargas asíncronas

Toda función `async loadX(...)` que actualiza estado de UI con la respuesta del backend **debe** protegerse contra race conditions con un token monótono creciente. El token se incrementa al iniciar la llamada y se compara al volver del `await`: si el token capturado ya no es el actual, la respuesta es obsoleta y se descarta sin mutar estado.

### Problema que resuelve

Si el usuario cambia de página, aplica un filtro, o reintenta rápidamente, varias llamadas pueden quedar en vuelo. **No hay garantía** de que la última que se inicia sea la última que vuelve — una red lenta puede entregar la respuesta vieja después de la nueva, y el contenido obsoleto pisa al actualizado. El bug es silencioso y reproducible.

### Patrón obligatorio

```ts
data() {
  return {
    // ...
    loadingStatus: LOADING_DATA_STATUS.DataNotLoaded as number,
    /**
     * Monótono creciente; cada carga toma su valor y descarta el
     * resultado si al volver del backend ya hay una carga más reciente
     * en vuelo.
     */
    loadToken: 0,
  }
},

methods: {
  async load(page: number): Promise<void> {
    const token = ++this.loadToken
    this.loadingStatus = LOADING_DATA_STATUS.DataLoading
    try {
      const response = await getX({ page, /* ... */ })
      if (token !== this.loadToken) return   // ← descarta respuesta obsoleta
      this.items = response.items
      this.pagination = response.pagination
      this.currentPage = response.pagination.page
      this.loadingStatus = LOADING_DATA_STATUS.DataLoaded
    } catch {
      if (token !== this.loadToken) return   // ← también en catch
      this.loadingStatus = LOADING_DATA_STATUS.DataError
    }
  },
},
```

Reglas:

- El `++this.loadToken` se hace **síncronamente** antes del primer `await`, así una llamada posterior incrementa el token antes de que la anterior vuelva.
- El guard `if (token !== this.loadToken) return` va **después de cada `await`** que pueda mutar estado de UI, incluyendo el `catch` — un error tardío de un request descartado no debe contaminar `loadingStatus` ni `loadError`.
- En composables (`setup`/`function`), `loadToken` es una variable `let` en el closure, no un `ref` (no necesita ser reactivo).

### `loadKey` — companion para consolidar inputs reactivos

Cuando varios inputs reactivos pueden disparar recarga (`pageSize`, `filters`, `companyId`, `siteId`, etc.), **un único watcher sobre una computed `loadKey`** evita dobles disparos cuando varios inputs cambian en el mismo tick:

```ts
computed: {
  /** Inputs reactivos que disparan recarga. Una sola fuente de cambio. */
  loadKey(): string {
    return [
      this.siteId,
      this.companyId,
      this.pageSize,
      JSON.stringify(this.extraFilters ?? {}),
    ].join('|')
  },
},

watch: {
  loadKey() {
    this.load(1)
  },
},

mounted() {
  this.load(1)                              // carga inicial
},
```

Reglas:

- El watcher de `loadKey` es **no-immediate**: la carga inicial sigue siendo responsabilidad de `mounted()` (consistente con §11.13).
- `loadKey` debe ser determinista: si los inputs no cambian semánticamente, la clave no cambia. Para objetos planos, `JSON.stringify` es adecuado; para estructuras grandes, usar un hash más eficiente.
- Vue Options API deduplica computeds en el mismo tick — un solo `loadKey` cambia una vez aunque varios inputs muten simultáneamente; en cambio, watchers separados sobre cada input se disparan individualmente.

### Cuándo aplica cada pieza

| Situación                                                | `loadToken` | `loadKey` |
|----------------------------------------------------------|:-----------:|:---------:|
| Tabla/listado sin props reactivos (sólo paginación)      |      ✅      |     —     |
| Tabla con un único input reactivo                        |      ✅      |     —     |
| Tabla con múltiples inputs reactivos                     |      ✅      |     ✅     |
| Composable que expone `load(page)`                       |      ✅      |     —     |
| Vista de detalle con `routeId` (ver §11.12)              |  opcional¹  |     —     |

¹ En `DetailsView` el riesgo es menor porque la navegación entre routes es relativamente lenta; pero si la vista se reusa entre rutas (Vue Router cachea instancias con `<keep-alive>`), `loadToken` cierra el caso.

### Anti-patrones

- ❌ **Watchers duplicados con el mismo body** — `companyId() { this.load(1) }` + `siteId() { this.load(1) }` produce dos llamadas idénticas cuando ambos cambian en el mismo tick. Consolidar con `loadKey`.
- ❌ **Sin guard tras el `await`** — `this.items = response.items` sin verificar token; la respuesta vieja puede sobrescribir a la nueva.
- ❌ **Guard sólo en el branch `try`** — el `catch` también debe verificar token; un error tardío no debe poner `loadingStatus = DataError` si la carga vigente ya terminó OK.
- ❌ **Resetear `loadToken` al desmontar** — no es necesario; el componente desmontado no muta su propio estado.

### Referencia de implementación

`modules/terminals/components/TerminalsTable.vue` aplica el patrón completo (`loadToken` + `loadKey` consolidando `siteId`, `companyId`, `pageSize`, `extraFilters`). Las 7 tablas restantes (`SitesTable`, `DeviceListView`, `CompanyListView`, `EmployeeListView`, `BlacklistEntryListView`, `BlacklistMovementListView`, y el composable `useFareCategoryList`) replican el patrón.

---

## 11.15 Navegación consciente del origen ("Volver" y breadcrumb dinámicos)

Cuando una misma vista de detalle es alcanzable desde **varias** vistas de origen (p. ej. el detalle de un dispositivo se abre tanto desde el inventario global como desde el detalle de un lote), el botón "Volver" y el breadcrumb **no deben** apuntar a una ruta fija. Deben regresar a la vista que realmente invocó al detalle, conservando sus `params`/`query`.

El patrón se apoya en tres helpers genéricos y agnósticos de dominio en `core/utils/NavigationUtils.ts` (tipos `BreadcrumbItem` y `OriginRoute` en `core/types/Nav.ts`):

- `captureOriginRoute(from)` — se llama dentro de `beforeRouteEnter`; devuelve la ruta de origen (`{ name, params, query }`) o `null` en entrada directa por URL.
- `originLocation(origin, fallback)` — convierte el origen en destino de navegación para "Volver", o el `fallback` si no hay origen.
- `resolveBreadcrumb(origin, current, trailsByOrigin, fallbackTrail)` — construye el breadcrumb seleccionando la cadena según el `name` del origen, y añade el item de la vista actual al final.

### Patrón canónico

```ts
import { captureOriginRoute, originLocation, resolveBreadcrumb }
  from '@/core/utils/NavigationUtils'
import type { BreadcrumbItem, OriginRoute } from '@/core/types/Nav'

export default defineComponent({
  data() {
    return {
      // null en navegación directa por URL → se usa el fallback.
      origin: null as OriginRoute | null,
    }
  },

  // beforeRouteEnter corre sin instancia: se captura `from` y se asigna
  // a la instancia en el callback de `next`.
  beforeRouteEnter(_to, from, next) {
    const origin = captureOriginRoute(from)
    next((vm) => {
      ;(vm as { origin: OriginRoute | null }).origin = origin
    })
  },

  computed: {
    breadcrumb(): BreadcrumbItem[] {
      return resolveBreadcrumb(
        this.origin,
        { label: this.headerTitle },              // item actual (sin `to`)
        {
          // Cadena por cada origen conocido. La clave es el `name` de ruta.
          'devices-batches-detail': (origin) => [
            { label: 'Lotes de Inventario', to: { name: 'devices-batches' } },
            {
              label: `Lote ${origin.query.reference ?? ''}`,
              to: { name: 'devices-batches-detail', params: origin.params, query: origin.query },
            },
          ],
        },
        // fallbackTrail: orígenes no mapeados + entrada directa por URL.
        [{ label: 'Inventario', to: { name: 'devices-inventory' } }],
      )
    },
  },

  methods: {
    onBack(): void {
      this.$router.push(originLocation(this.origin, { name: 'devices-inventory' }))
    },
  },
})
```

Reglas:

- **Capturar siempre en `beforeRouteEnter`**, nunca en `mounted()` — fuera de los guards de navegación Vue Router no expone la ruta previa de forma fiable.
- **`origin` puede ser `null`** (entrada directa por URL, recarga, enlace externo). El `fallback`/`fallbackTrail` cubre ese caso y nunca debe omitirse.
- **El item actual va sin `to`** — representa la posición actual y no debe ser un enlace.
- **No usar `$router.back()`** como sustituto: no diferencia orígenes mapeados de externos, no permite reconstruir la cadena del breadcrumb y rompe en recarga directa.

### Anti-patrón

```ts
// ❌ Destino fijo: ignora desde dónde se llegó.
onBack(): void {
  this.$router.push({ name: 'devices-inventory' })
},
// ❌ Breadcrumb hardcodeado en el template, idéntico venga de donde venga.
:breadcrumb="[{ label: 'Inventario', to: { name: 'devices-inventory' } }, { label: headerTitle }]"
```

### Referencia de implementación

`modules/devices/views/DeviceDetailsView.vue` aplica el patrón completo (origen = inventario global o detalle de lote, con el `?reference=` del lote conservado en el enlace intermedio del breadcrumb).

---

# 12. Sistema de Permisos (RBAC)

El control de acceso por rol se modela como una matriz de permisos `<module>:<action>` sobre los módulos del proyecto. Toda nueva ruta, item del sidebar o acción de escritura debe declarar su permiso explícitamente; el default es **denegar acceso**, no permitirlo.

## 12.1 Modelo de permisos

- Notación obligatoria: strings `<module>:<action>` (ej. `accounts:view`, `fares:edit`).
- Granularidad por módulo: **`view`** (leer listados y detalles) vs. **`edit`** (cubre create + update + delete).
- Catálogo único en `src/modules/auth/constants/role-permissions.ts`:
  - `MODULES` — lista canónica de módulos sometidos a RBAC. Cuando se crea un nuevo módulo con rutas privadas, añadirlo aquí antes de declarar permisos.
  - `Permission` — tipo template literal `${Module}:${Action}` que ata los strings al catálogo en tiempo de compilación.
  - `ROLE_PERMISSIONS` — fallback local rol → permisos; usado cuando el backend no entrega `permissions[]`.
  - `resolvePermissionsForRole(role)` — única función que resuelve el fallback. No reproducir esta lógica en otras capas.

> El backend es la fuente de verdad. El fallback existe sólo para sesiones donde `LoginResult.permissions` viene vacío.

## 12.2 Estado: auth store

`AuthSession.permissions: string[]` es la **única fuente de verdad** en cliente. Reglas obligatorias del `auth.store.ts`:

- Persistir siempre `permissions` en `localStorage` junto al resto de la sesión.
- En `setSession(next)`: si `next.permissions` viene poblado (array no vacío) se usa tal cual; si no, derivar con `resolvePermissionsForRole(next.role)` y persistir el resultado.
- En `loadFromStorage()`: sanitizar `permissions` (filtrar a strings); si queda vacío, derivar del `role` recuperado. Esto garantiza que sesiones persistidas antes de que el backend entregara `permissions` sigan funcionando.
- No leer `localStorage` desde otras capas — siempre vía el store.

## 12.3 API de consumo: `usePermissions`

Toda lectura de permisos en composables, vistas o componentes pasa por el composable `src/modules/auth/composables/usePermissions.ts`:

```ts
const { can, canAny, canAll, canView, canEdit, permissions } = usePermissions()
```

Reglas:

- **No** acceder a `auth.permissions` directamente desde una vista — usar siempre `usePermissions`.
- En Options API, instanciar en `data()` (`perms: usePermissions()`) y consumir vía `this.perms.can(...)`.
- Para lookups masivos (sidebars, tablas), `usePermissions` ya memoiza un `Set` interno: no recrear el `Set` localmente.

## 12.4 Directiva `v-can`

Para gating visual de botones/elementos. Sintaxis soportada en `src/app/directives/can.ts`:

| Forma                | Significado                                  |
|----------------------|----------------------------------------------|
| `v-can="'accounts:edit'"`                    | requiere el permiso indicado |
| `v-can:any="['a:edit','b:edit']"`            | basta con cualquiera         |
| `v-can:all="['a:view','a:edit']"`            | requiere todos               |

Reglas:

- La directiva alterna `el.style.display = 'none'` — **no remueve del DOM**. Esto soporta `<template v-can>` y mantiene el ciclo de vida de los componentes hijos.
- La seguridad real vive en backend + guard de ruta. `v-can` es UX, no defensa.
- Si la condición de gating también se necesita en TypeScript (p. ej. para `readonly` de un formulario compartido), exponer un computed que use `usePermissions` y reusar el flag desde la directiva y el TS.

## 12.5 Guard de navegación

`router.beforeEach` en `src/app/router/index.ts` evalúa permisos sobre `to.meta`:

- Rutas públicas se marcan con `meta: { public: true }` y se saltan el chequeo de autenticación y permisos. Aplica a `authRoutes`, `notFoundRoutes` y cualquier ruta que pueda accederse sin login.
- Rutas privadas requieren `meta.permission: '<module>:<action>'` **o** `meta.permissionAny: ['<perm1>','<perm2>']`. Si faltan, la ruta queda accesible a todo usuario autenticado.
- Acceso denegado → **redirect silencioso a `dashboard`** (`{ name: 'dashboard' }`). No mostrar pantallas de "403".
- `dashboard` **nunca** declara `meta.permission` — es el fallback universal al que se redirige cuando un usuario intenta entrar a un módulo prohibido.

### 12.5.1 Convención por tipo de ruta

| Ruta                          | `meta.permission`              |
|-------------------------------|--------------------------------|
| Listado (`<feature>`)         | `'<module>:view'`              |
| Detalle (`<feature>-detail`)  | `'<module>:view'`              |
| Creación (`<feature>-create`) | `'<module>:edit'`              |
| Formularios compartidos detalle/edición (caso `fares`) | `'<module>:view'` en la ruta + gating del botón Guardar con `v-can="'<module>:edit'"` |

Cuando se agrega un módulo nuevo:

1. Añadir el módulo a `MODULES` en `role-permissions.ts`.
2. Asignar las dos entradas (`view`/`edit`) en `ROLE_PERMISSIONS` para los roles existentes.
3. Declarar `meta.permission` en cada ruta del módulo.
4. Añadir el item al menú con `permission` (ver §12.6).

## 12.6 Sidebar filtrado

`src/core/types/Nav.ts` extiende `NavItem` con `permission?: string` y `permissionAny?: string[]`. `src/core/constants/menu.ts` declara los permisos por item alineados a la ruta destino.

`AppSidebar.vue` no consume `sidebarMenuGroups` directamente: usa un computed `groups` que:

1. Filtra items hoja por `permission` / `permissionAny`.
2. Para items con `children`: filtra los hijos y oculta el padre si quedan 0 visibles.
3. Oculta grupos completos sin items visibles.

Reglas:

- Items sin `permission` quedan visibles a todos los autenticados — reservar este caso a `Dashboard` y secciones de desarrollo (`Catálogo UI`).
- El permiso del item debe coincidir con `meta.permission` de la ruta a la que enlaza. Si difieren, el usuario puede ver un enlace que lo redirigirá al dashboard.

## 12.7 Adopción en vistas

Patrón obligatorio para botones de crear/editar/eliminar:

```html
<ActionButton v-can="'accounts:edit'" @click="onCreate">
  Añadir cuenta
</ActionButton>
```

Cuando la misma vista mezcla lectura y edición (detalle con edición inline, formulario compartido detail/edit en `fares`):

```ts
computed: {
  canEditAccount(): boolean {
    return this.perms.canEdit('accounts')
  },
  readonly(): boolean {
    return !this.canEditAccount
  },
},
```

- Pasar `:readonly="readonly"` al formulario para deshabilitar inputs.
- Ocultar **Guardar** con `v-can="'<module>:edit'"` o `v-if="canEdit<Module>"`.
- **No** ocultar la vista completa: el usuario con `:view` debe poder leer.

## 12.8 Anti-patrones

- ❌ Leer `auth.session?.permissions` directamente desde una vista — usar `usePermissions`.
- ❌ Construir `new Set(auth.permissions)` ad-hoc en cada componente — `usePermissions` ya lo hace.
- ❌ Hardcodear roles (`if (auth.role === 'admin')`) — evaluar permisos, no roles. El rol es un input para el fallback; la decisión se toma sobre `permissions[]`.
- ❌ Declarar una ruta privada sin `meta.permission` — queda accesible a cualquier autenticado.
- ❌ Mostrar pantallas de "403" o "No autorizado" — la convención es redirect silencioso a `dashboard`.
- ❌ Eliminar elementos del DOM como sustituto de `v-can` (`v-if="perms.can(...)"` en cada botón) — usar la directiva, que ya maneja el caso de forma uniforme.
- ❌ Replicar la matriz de roles → permisos fuera de `role-permissions.ts`.

---

# 13. Principios Finales

## Regla principal

### “La lógica vive fuera de la UI”

y

### “Los componentes reutilizables no conocen el negocio”

---

# 14. Resultado Esperado

La aplicación final debe ser:

- escalable
- mantenible
- consistente
- desacoplada
- reutilizable
- fácil de testear
- fácil de extender
- preparada para equipos grandes y aplicaciones enterprise
