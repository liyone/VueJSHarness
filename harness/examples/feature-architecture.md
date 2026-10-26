# A passenger search feature

Illustrative Vue 3.5 snippets, not an application to scaffold. Assume a browser-rendered feature with an API returning `{ id: string, name: string }[]`. Adapt the endpoint, authentication, and domain contract to the existing application.

```text
features/passengers/
  PassengersPage.vue
  usePassengers.ts
  components/PassengerTable.vue
```

The page owns the query and selected ID and coordinates what is visible. The composable owns request state and cancellation. The table renders rows and emits an ID; it does not fetch or modify records. A table this small could stay inline in the page; extraction here makes the contract visible, not mandatory.

## Stateful boundary: usePassengers.ts

Use the complete `usePassengers` implementation in [reliability.md](../standards/reliability.md#a-bounded-request-example) as this file. It exports `Passenger` alongside the composable, accepts a reactive getter, and returns readonly refs named `passengers`, `loading`, and `error`. Keep request ownership in that implementation rather than duplicating it here.

The composable runs immediately, treats a blank normalized query as idle, clears old rows during a new search, and guards all asynchronous writes. Its synchronous setup watcher owns cleanup on input change and unmount. The page does not add another watcher to coordinate those responsibilities.

## Coordinator: PassengersPage.vue

```vue
<script setup lang="ts">
import { computed, ref } from 'vue'
import PassengerTable from './components/PassengerTable.vue'
import { usePassengers } from './usePassengers'

const query = ref('')
const selectedId = ref<string | null>(null)
const { passengers, loading, error } = usePassengers(() => query.value)

const selectedPassenger = computed(() =>
  passengers.value.find((passenger) => passenger.id === selectedId.value),
)
const status = computed(() => {
  if (!query.value.trim()) return 'Enter a passenger name.'
  if (loading.value) return 'Loading passengers…'
  if (error.value) return ''
  return passengers.value.length === 0
    ? 'No passengers found.'
    : `${passengers.value.length} passengers found.`
})
</script>

<template>
  <section aria-labelledby="passenger-heading">
    <h1 id="passenger-heading">Passengers</h1>
    <label for="passenger-query">Search by name</label>
    <input id="passenger-query" v-model="query" type="search" />

    <p role="status">{{ status }}</p>
    <p v-if="error" role="alert">{{ error }}</p>

    <PassengerTable
      v-if="!loading && !error && passengers.length > 0"
      :passengers="passengers"
      :selected-id="selectedId"
      @select="selectedId = $event"
    />
    <p v-if="selectedPassenger">Selected: {{ selectedPassenger.name }}</p>
  </section>
</template>
```

The selected ID is local UI state, so it stays in the page. Selection details appear only when that ID belongs to current results; if the same ID reappears it remains selected. Choose a different reset policy if the actual feature requires one. Search runs as the normalized input changes; debounce is a product/performance decision to add when needed.

## Presentational boundary: components/PassengerTable.vue

```vue
<script setup lang="ts">
import type { Passenger } from '../usePassengers'

defineProps<{
  passengers: readonly Readonly<Passenger>[]
  selectedId: string | null
}>()
const emit = defineEmits<{
  select: [id: string]
}>()
</script>

<template>
  <table>
    <caption>Matching passengers</caption>
    <thead>
      <tr>
        <th scope="col">Name</th>
        <th scope="col">Selection</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="passenger in passengers" :key="passenger.id">
        <td>{{ passenger.name }}</td>
        <td>
          <button
            type="button"
            :aria-label="`Select ${passenger.name}`"
            :disabled="passenger.id === selectedId"
            @click="emit('select', passenger.id)"
          >
            {{ passenger.id === selectedId ? 'Selected' : 'Select' }}
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</template>
```

The props contract accepts the composable's readonly rows. Selection changes the owner's ID through a typed event. Semantic buttons supply keyboard activation, and text is interpolated rather than treated as HTML. This page assumes one instance; repeated embedded instances need unique control/heading IDs.

## Behavior to protect

Use existing tools and the [testing decision guide](../standards/testing.md): search success/failure/empty states, selection's emitted ID, latest-query ownership after out-of-order completion, and disposal while a request is pending. Manually inspect keyboard operation and status feedback where the existing suite does not cover them. These snippets have no configured Vue build or test runner in the harness repository.

No store, service layer, separate types module, or generic search framework is needed for this example. Introduce a boundary later only if actual consumers or responsibilities justify it under the [architecture guide](../standards/vue-architecture.md).
