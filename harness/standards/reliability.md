# Reliability

Use this reference when changing requests, asynchronous state, side effects, or user-facing failure behavior. Examples target Vue 3.5 and browser-native APIs.

## Requests have an owner

Reuse a suitable existing API client; otherwise `fetch` is sufficient for ordinary requests. Check `response.ok`: HTTP failures do not reject the fetch promise. Handle network errors, unsuccessful status codes, invalid JSON, and an unexpected response shape at the boundary. TypeScript assertions do not validate server data. [Fetch behavior](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

Specify what the user sees while loading, after failure, and when a successful result is empty. Empty is not an error. Decide whether old results remain visible during refresh; if retained, label them appropriately. Give users a practical recovery action when needed. Avoid automatic retries for mutations without understanding duplicate-submission and server idempotency behavior.

Common mistake:

```ts
watch(query, async (value) => {
  results.value = await search(value)
}) // A slow old request can overwrite the latest result.
```

Cancellation saves work; an ownership guard prevents stale writes even when a dependency ignores cancellation or additional asynchronous processing has begun. Guard success, failure, and loading completion. This is an application design rule, not a guarantee supplied by Vue.

## A bounded request example

This browser-side `usePassengers.ts` owns one search at a time. Call it synchronously from component setup, or within an effect scope that the caller stops. Its API is used by the [feature example](../examples/feature-architecture.md). The endpoint and payload are illustrative; reuse the application's authentication and API contract. SSR applications should use their existing server-data lifecycle instead of copying a browser-only relative request.

```ts
import { readonly, ref, watch } from 'vue'

export type Passenger = { id: string; name: string }

function isPassenger(value: unknown): value is Passenger {
  return typeof value === 'object' && value !== null
    && 'id' in value && typeof value.id === 'string'
    && 'name' in value && typeof value.name === 'string'
}

export function usePassengers(query: () => string) {
  const passengers = ref<Passenger[]>([])
  const loading = ref(false)
  const error = ref<string | null>(null)

  watch(() => query().trim(), async (search, _previous, onCleanup) => {
    passengers.value = [] // This feature clears old results on a new search.
    error.value = null
    loading.value = false
    if (!search) return

    const controller = new AbortController()
    let active = true
    const isCurrent = () => active && query().trim() === search
    onCleanup(() => {
      active = false
      controller.abort()
    }) // Register before the first await.

    loading.value = true
    try {
      const params = new URLSearchParams({ q: search })
      const response = await fetch(`/api/passengers?${params}`, {
        signal: controller.signal,
      })
      if (!response.ok) throw new Error(`HTTP ${response.status}`)
      const body: unknown = await response.json()
      if (!Array.isArray(body) || !body.every(isPassenger)) {
        throw new Error('Unexpected passenger response')
      }
      if (isCurrent()) passengers.value = body
    } catch {
      if (isCurrent() && !controller.signal.aborted) {
        error.value = 'Passengers could not be loaded. Try another search.'
      }
    } finally {
      if (isCurrent()) loading.value = false
    }
  }, { immediate: true })

  return {
    passengers: readonly(passengers),
    loading: readonly(loading),
    error: readonly(error),
  }
}
```

Changing the query invalidates the previous run. Clearing it also cancels pending work and restores the idle state. The getter comparison rejects a result if the input changed before the queued watcher reran. Normalizing whitespace is a deliberate search policy, not a requirement for all inputs.

Vue stops watchers created synchronously in setup when their component unmounts; their registered cleanup invalidates and aborts the pending run. Watchers created later in asynchronous callbacks need explicit stopping. The callback-provided `onCleanup` above works in Vue 3.5. The alternative `onWatcherCleanup` must be called synchronously before any `await`. [Watcher cleanup and stopping](https://vuejs.org/guide/essentials/watchers.html#side-effect-cleanup)

For timers, listeners, observers, or connections outside a watcher, pair registration with `onUnmounted` or `onScopeDispose` in the same owner. Remove the exact listener you registered and clear pending timers. Cancellation does not undo a mutation already accepted by the server. Use pending-state protection and the existing server contract for submissions. Add debounce, caching, sharing, or retry policies only for a demonstrated need.

## Security and accessibility at the changed boundary

- Render ordinary server/user text through interpolation. Do not compile untrusted templates or pass untrusted content to `v-html`. For a justified rich-text requirement, use the application's reviewed sanitization policy. Validate allowed URL schemes and destinations where user data controls links. [Vue security](https://vuejs.org/guide/best-practices/security.html)
- Keep secrets out of frontend code and logs. UI visibility and client validation are not authorization; the server must enforce permissions and validate writes. Preserve the existing authentication and CSRF protections. Report failures without exposing sensitive response bodies.
- Prefer semantic buttons, links, forms, and tables. Give controls accessible labels, preserve keyboard operation and visible focus, associate validation errors with their fields, and announce meaningful asynchronous status without repeatedly interrupting users. Check focus after dialogs or navigation when those behaviors change. [Vue accessibility](https://vuejs.org/guide/best-practices/accessibility.html)

Use the [testing guide](testing.md) to choose coverage for failure, reordering, cancellation, and cleanup rather than testing internal flags.
