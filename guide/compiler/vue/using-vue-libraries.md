---
outline: deep
---

# Using Vue libraries

The Unison (Vue) framework leverages most of the Vue Composition API, allowing you to use Vue libraries with minimal configuration in your React projects. To set this up, add the following lines to your `package.json` :

```json
{
  // for npm
  "overrides": {
    "vue-demi": "npm:@unisonjs/vue",
    "vue": "npm:@unisonjs/vue"
  },
  // for yarn/pnpm
  "resolutions": {
    "vue-demi": "npm:@unisonjs/vue",
    "vue": "npm:@unisonjs/vue"
  }
}
```

If you're using vite, you also need to exclude `@unisonjs/vue`, `vue` and `vue-demi` from `optimizeDeps`

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  optimizeDeps: {
    exclude: ["vue-demi", "vue", "@unisonjs/vue"],
  },
});
```
---

## Using Vue Plugins

Some Vue libraries require attaching a plugin to your application. Here’s how you can integrate a Vue plugin using the Unison (Vue) framework in React :

1. **Create an App Context**

```js
const app = createApp();
```

2. **Use the Vue Plugin**

```js
app.use(vuePlugin);
```

3. **Wrap Your React App with `AppProvider`**

```js
root.render(
  <AppProvider app={app}>
    <App />
  </AppProvider>
);
```

Here’s an example using Pinia as the Vue state management library:

```jsx
import ReactDOM from 'react-dom/client';
import { createPinia } from 'pinia';
import { AppProvider, createApp } from '@unisonjs/vue';

const pinia = createPinia();
const app = createApp();

app.use(pinia);

const root = ReactDOM.createRoot(document.getElementById('root')!);

root.render(
  <AppProvider app={app}>
    <App />
  </AppProvider>,
);
```

::: warning
Using Vue libraries with SSR (Server-Side Rendering) does not currently work as expected. This issue is under investigation, and a workaround will be provided in the near future.
:::

## But why using vue libraries in a React project ???

### Remove overhead

The Unison framework leverage the integration of React Hook however to do so, no only it needs a extra work to enable state tracking and notify changes but also it prevents the Unison framework to overlap React mechanism.

Using Vue libraries instead of React libraries can significantly reduce overhead by eliminating the need for React primitives such as `useState`, `useEffect` and so on.

::: details Details
![image info](/using-vue-lib.png)
:::

## Vue code-sharing

Using Vue libraries can be advantageous for code-sharing between Vue and React projects. For example, you might use Vue for your web app and React Native for your mobile app, leveraging a shared state management library like Pinia across both platforms. This approach allows you to maintain a consistent state management solution while only needing to adapt the templating.
