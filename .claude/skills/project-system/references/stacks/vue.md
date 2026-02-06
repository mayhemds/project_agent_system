# Vue.js Reference

## Component (Script Setup)

```vue
<script setup lang="ts">
import { ref, computed, onMounted } from 'vue';
const props = defineProps<{ title: string; count?: number }>();
const emit = defineEmits<{ (e: 'update', value: number): void }>();
const items = ref<string[]>([]);
const itemCount = computed(() => items.value.length);
function addItem(item: string) {
  items.value.push(item);
  emit('update', items.value.length);
}
onMounted(() => { /* DOM ready */ });
</script>
<template>
  <h2>{{ title }} ({{ itemCount }})</h2>
  <slot />
</template>
```

## Reactivity

```ts
import { ref, reactive, computed, watch, watchEffect } from 'vue';

// ref: primitives and replaceable values (.value in script)
const count = ref(0);
count.value++;

// reactive: objects (deeply reactive, no .value)
const state = reactive({ user: { name: 'Alice' }, items: [] as string[] });
state.user.name = 'Bob';

// computed: cached derived values
const doubled = computed(() => count.value * 2);

// watch: react to specific changes (old + new values)
watch(count, (newVal, oldVal) => { console.log(newVal); });
watch(() => state.user, (val) => { /* ... */ }, { deep: true });

// watchEffect: auto-tracks deps, runs immediately
watchEffect(() => { console.log(count.value); });
```

## Template Refs

```vue
<script setup lang="ts">
const inputRef = ref<HTMLInputElement | null>(null);
onMounted(() => inputRef.value?.focus());
</script>
<template><input ref="inputRef" /></template>
```

## Event Handling

```vue
<template>
  <button @click="handleClick">Click</button>
  <form @submit.prevent="onSubmit">
    <input @keyup.enter="search" v-model.trim="query" />
    <input v-model.number="age" type="number" />
  </form>
</template>
```

## Composables

```ts
// composables/useFetch.ts -- reusable logic with `use` prefix
export function useFetch<T>(url: string | Ref<string>) {
  const data = ref<T | null>(null);
  const error = ref<string | null>(null);
  const loading = ref(false);
  async function fetchData() {
    loading.value = true;
    try {
      data.value = await fetch(typeof url === 'string' ? url : url.value).then(r => r.json());
    } catch (e) { error.value = (e as Error).message; }
    finally { loading.value = false; }
  }
  watchEffect(fetchData);
  return { data, error, loading, refetch: fetchData };
}
```

## Pinia Store

```ts
import { defineStore } from 'pinia';
import { ref, computed } from 'vue';

export const useAuthStore = defineStore('auth', () => {
  const user = ref<User | null>(null);
  const token = ref(localStorage.getItem('token'));
  const isAuthenticated = computed(() => !!token.value);

  async function login(email: string, password: string) {
    const res = await fetch('/api/login', {
      method: 'POST', body: JSON.stringify({ email, password }),
      headers: { 'Content-Type': 'application/json' },
    });
    const data = await res.json();
    token.value = data.token; user.value = data.user;
    localStorage.setItem('token', data.token);
  }

  function logout() { token.value = null; user.value = null; localStorage.removeItem('token'); }
  return { user, token, isAuthenticated, login, logout };
});
```

## Vue Router

```ts
import { createRouter, createWebHistory } from 'vue-router';
const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/', component: () => import('./views/Home.vue') },
    { path: '/dashboard', component: () => import('./views/Dashboard.vue'),
      meta: { requiresAuth: true },
      children: [{ path: 'settings', component: () => import('./views/Settings.vue') }],
    },
    { path: '/user/:id', component: () => import('./views/User.vue'), props: true },
    { path: '/:pathMatch(.*)*', component: () => import('./views/404.vue') },
  ],
});
router.beforeEach((to) => {
  if (to.meta.requiresAuth && !useAuthStore().isAuthenticated) return '/login';
});
// In components: useRoute().params.id, useRouter().push('/dashboard')
```

## Nuxt.js Integration

```ts
// Nuxt auto-imports Vue APIs. File routing: pages/users/[id].vue -> /users/:id
const { data } = await useFetch('/api/users');
useHead({ title: 'Page Title', meta: [{ name: 'description', content: '...' }] });
// Server routes: server/api/users.get.ts
export default defineEventHandler(async () => ({ users: [] }));
```

## Lifecycle Hooks

`onMounted`, `onUnmounted` (cleanup), `onUpdated`, `onBeforeMount`, `onBeforeUnmount`

```ts
onMounted(() => window.addEventListener('resize', onResize));
onUnmounted(() => window.removeEventListener('resize', onResize));
```

## Key Conventions

- Use `<script setup>` for all components: less boilerplate, better types
- `ref` for primitives, `reactive` for objects you will not reassign
- Composables use `use` prefix: `useFetch`, `useAuth`, `useTheme`
- Pinia: one store per domain, composition API style
- Lazy-load routes with `() => import()` for code splitting
- `v-model` for forms; custom components support it via `defineModel()`
- Prefer `watchEffect` for side effects; `watch` when you need old/new values
