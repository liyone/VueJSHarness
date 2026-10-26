# Vue architecture

Use this when deciding component responsibilities, feature boundaries, or state ownership. Target Vue 3.5; improve the code involved in the request without treating surrounding legacy code as an architectural endorsement.

## Keep the feature understandable

Prefer a main single-file component that coordinates the feature, a composable when meaningful stateful logic warrants one, and presentational components where they help. This is an example, not a required directory contract:

```text
features/passengers/
  PassengersPage.vue
  usePassengers.ts
  components/
    PassengerTable.vue
```

The page connects state to the UI, composes children, and handles feature-level events. A few refs, computed values, and handlers can stay there. Extract code when its responsibility becomes independently understandable or testable, not when the file crosses a line count. A cohesive 300-line component can be clearer than six fragments.

## Choose a boundary for a reason

| Keep or introduce | When it helps |
| --- | --- |
| Component-local logic | State and behavior belong to one small UI interaction. |
| Feature composable | API-backed state, coordinated async work, complex reactive behavior, lifecycle ownership, reuse, or independent testing forms a meaningful responsibility. |
| Presentational component | A UI unit becomes easier to read, reuse, or test through an explicit props/events contract. Local UI state is fine; substantial business rules belong with their owner. |
| Plain function | A calculation or transformation needs no reactive state or lifecycle. |
| API client, store, or shared module | Existing consumers or a concrete cross-feature concern justify it. |

Do not require a service, store, types file, constants file, utility file, barrel, adapter, or repository layer for every feature. Keep types and helpers beside their use until another consumer or responsibility makes a separate module useful. Avoid components whose only purpose is forwarding every prop and event.

## Justify dependencies before adding them

Consider Vue, browser-native APIs, and suitable production dependencies first. Popularity alone does not justify Pinia, TanStack Query, Axios, Zod, VueUse, form/UI libraries, state management, or new testing libraries. Before proposing a package, explain:

1. The concrete problem it solves.
2. Why Vue, browser APIs, and existing dependencies are insufficient.
3. The configuration, concepts, and integration complexity it adds.
4. Its maintenance burden, including upgrades and security fixes.
5. Why the benefit outweighs those costs for this project.

Obtain approval before adding a dependency unless the current task already explicitly authorizes it. A tooling gap may justify a recommendation; adoption remains an explicit engineering decision. This standalone harness adds no packages or package manifest.

## Make state ownership explicit

- Keep state with the narrowest owner that coordinates its consumers. Lift it to a common parent when siblings genuinely share it.
- Use props for inputs and events for child-to-parent requests. A callback prop can express a deliberate supplied operation; do not provide both an event and callback for the same action without a reason.
- Do not mutate props, including nested objects and arrays. Emit the intended change for the owner to apply. A local editable draft needs an explicit save/cancel and incoming-update policy; a copied prop does not stay synchronized automatically.
- Creating refs inside a composable gives each call its own state. Module-scope refs share state across consumers; introduce that only intentionally. In SSR, shared mutable module state can also cross requests.
- Reuse suitable existing state tools when sharing warrants them. Ordinary feature-local state needs no global store.

Vue's [one-way prop flow](https://vuejs.org/guide/components/props.html#one-way-data-flow) explains the runtime boundaries, including nested mutation risks. See [reactivity](vue-reactivity.md) for reactive inputs and safe destructuring.

## Keep contracts small and typed

Prefer Composition API and `<script setup lang="ts">` for new components. This presentational fragment receives rows and requests a selection; it does not fetch or modify them:

```vue
<script setup lang="ts">
defineProps<{
  rows: readonly { id: string; name: string }[]
}>()
const emit = defineEmits<{
  select: [id: string]
}>()
</script>

<template>
  <ul>
    <li v-for="row in rows" :key="row.id">
      <button type="button" @click="emit('select', row.id)">
        {{ row.name }}
      </button>
    </li>
  </ul>
</template>
```

Compiler macros need no imports. Typed props and emit tuples are supported in Vue 3.5; static types do not validate arbitrary API data. See the official [script setup](https://vuejs.org/api/sfc-script-setup.html) and [TypeScript guidance](https://vuejs.org/guide/typescript/composition-api.html#typing-component-emits).

A composable should name its responsibility and expose intentional state and operations, for example `passengers`, `loading`, `error`, and `reload`. Prefer a plain return object containing refs so callers can destructure it. Avoid a generic options-heavy abstraction until actual consumers need it. Vue's [composable conventions](https://vuejs.org/guide/reusability/composables.html#return-values) describe this return shape.

The [worked feature example](../examples/feature-architecture.md) shows these roles together. Consult [reliability](reliability.md) for requests and UI states, and [testing](testing.md) for behavior worth protecting.
