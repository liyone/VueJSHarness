# Vue 3.5 reactivity

Use this when changing reactive inputs, derived values, watchers, or lifecycle effects. These examples target Vue 3.5 and do not assume later APIs.

## Preserve the reactive connection

Prefer `ref` for individual state and values replaced as a whole. Use `reactive` for a cohesive object whose identity remains stable. In script, read and write refs with `.value`; templates unwrap top-level refs. Refs inside arrays and collections still require care with `.value`.

```ts
import { reactive, toRef } from 'vue'

const filters = reactive({ query: '' })

// Bad: a plain string snapshot, disconnected from later changes.
const { query } = filters

// Good: retain the property connection when a separate ref is useful.
const queryRef = toRef(filters, 'query')
// Or simply read filters.query where needed.
```

Reassigning a `reactive` variable to a new object disconnects consumers of the old proxy. Use `ref({ ... })` and replace `.value` when replacement is part of the design. Destructuring a plain object of refs returned by a composable preserves those refs. See [reactivity fundamentals](https://vuejs.org/guide/essentials/reactivity-fundamentals.html#limitations-of-reactive).

## Props destructuring changed in 3.5

Direct destructuring from `defineProps` in `<script setup>` is reactive in Vue 3.5 through compiler rewriting in that script block. This does **not** make ordinary object destructuring reactive or turn destructured props into refs.

```vue
<script setup lang="ts">
import { watch } from 'vue'

const { query = '' } = defineProps<{ query?: string }>()

// Bad: watch(query, ...) passes a string rather than a reactive source.
// Good: a getter preserves access to the changing prop.
watch(() => query, (value) => {
  console.log('Query changed:', value)
})

// Likewise, pass () => query to a composable that accepts a getter.
</script>
```

The consumer must read that getter inside `watch`, a computed getter, or a reactive effect. Calling it once during initialization takes a snapshot. `const props = defineProps<...>()` with `() => props.query` is also valid. Neither form permits prop mutation. See [reactive props destructure](https://vuejs.org/guide/components/props.html#reactive-props-destructure).

## Derive values; watch side effects

| Tool | Use it for |
| --- | --- |
| `computed` | A pure value derived from reactive state. No requests, writes to other state, or DOM mutations in the getter. |
| `watch` | An effect with explicit sources, old/new values, or controlled triggering. Useful for request inputs. |
| `watchEffect` | An effect whose synchronous reads naturally describe its dependencies. It runs immediately. |

```ts
import { computed, ref } from 'vue'

const firstName = ref('Ada')
const lastName = ref('Lovelace')

// Bad: another mutable ref plus watchers to keep fullName synchronized.
// Good: one derived value, with no synchronization path to maintain.
const fullName = computed(() => `${firstName.value} ${lastName.value}`)
```

Treat computed results as derived snapshots; change their source state instead of mutating the result. See [computed best practices](https://vuejs.org/guide/essentials/computed.html#best-practices).

`watchEffect` tracks reads only before the first `await`; a read after it does not establish a dependency. Prefer explicit `watch` sources when async dependencies would be unclear. A getter for the exact property usually communicates intent better than a deep watcher. Avoid watcher chains that merely synchronize duplicate state.

## Own timing and cleanup

- Register `onWatcherCleanup` synchronously inside the watcher, before the first `await`. It exists in Vue 3.5. The callback-provided `onCleanup` is also supported and is bound to the watcher; register it before starting work even though it lacks the same synchronous API restriction.
- Setup watchers created synchronously stop on unmount. Watchers created later in a timer or asynchronous callback need an explicitly owned stop handle. Prefer synchronous creation with conditional work inside.
- Watcher disposal does not automatically cancel arbitrary async work. Invalidate stale requests and guard their writes; use the complete pattern in [reliability](reliability.md).
- Default watchers run before their owner's DOM update. Use `flush: 'post'` when the effect needs updated DOM, or `nextTick` after a specific state change. Do not add `flush: 'sync'` by default; it bypasses batching.

See Vue's [watchers guide](https://vuejs.org/guide/essentials/watchers.html#side-effect-cleanup) for cleanup, dependency tracking, and timing.

Pair manual resources with teardown. For example, an anonymous listener cannot later be removed by passing a different function:

```ts
import { onMounted, onUnmounted, ref } from 'vue'

const viewportWidth = ref(0)
const updateWidth = () => { viewportWidth.value = window.innerWidth }

onMounted(() => {
  updateWidth()
  window.addEventListener('resize', updateWidth)
})
onUnmounted(() => {
  window.removeEventListener('resize', updateWidth)
})
```

Register hooks during setup. Create browser-only effects after mount where SSR applies. Clear timers, disconnect observers, and close owned subscriptions/connections when their lifetime ends. Keep cleanup with the code that acquires the resource. See [lifecycle hooks](https://vuejs.org/api/composition-api-lifecycle.html#onunmounted).
