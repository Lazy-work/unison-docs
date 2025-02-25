---
outline: deep
---

# Difference with React

## One-time execution

In React, every time a state changes, the function representing the component is re-executed entirely, unless you use React hooks like `useEffect` or `useMemo` to specify that some parts of your code should not run again or should run under certain conditions. Reactivity is enabled everywhere unless you opt out.

In the `Unison` context, your component runs only once unless you declare explicitly that's you want treat a piece of code as a side-effect. Reactivity is disable unless you opt-in.

```js
import { $unison, ref } from "@unisonjs/vue";

const Counter = $unison(() => {
  const count = ref(0);

  setInterval(() => count.value++, 1000); // Will not run again
  count.value++; // Will not break you app

  return () => (
    <div>
      <p>Count: {count.value}</p>
    </div>
  );
});
```

## Effect tracks dependencies automatically

When you create an effect in React, you must specify which state you want to track, to re-run the side-effect on change
In the Unison context, an effect track it's dependencies itself without needed a specification of your part

```js
import { $unison, ref, watchEffect } from "@unisonjs/vue";

const Counter = $unison(() => {
  const count = ref(0);

  watchEffect(() => {
    console.log(`Count has changed to: ${count.value}`); // Will log count changes
  });

  return () => (
    <div>
      <button onClick={() => count.value++}>Increment</button>
      <p>Count: {count.value}</p>
    </div>
  );
});
```

## No `useCallback` needed

Because a component runs only once, you can declare your callback in the component body and reference it in your JSX. No function will be recreated, and your JSX will keep the same reference. The state is accessed via a proxy (ref or reactive), so you can retrieve the current and updated state at any time.

```js
import { $unison, ref } from "@unisonjs/vue";

const Counter = $unison(() => {
  const count = ref(0);

  function increment() {
    count.value++;
  }

  return () => (
    <div>
      <button onClick={increment}>Increment</button>
      <p>Count: {count.value}</p>
    </div>
  );
});
```

## An effect can't

An effect that mutate its dependency won't trigger in the next cycle. A small security compare to `useEffect`.

```js
import { $unison, ref, watchEffect } from "@unisonjs/vue";

const Counter = $unison(() => {
  const count = ref(0);
  const doubleCount = ref(0);

  watchEffect(() => {
    // Will not run twice in a row
    // But this pattern, it's not recommanded anyway
    doubleCount.value = count.value * 2;
  });

  return () => (
    <div>
      <button onClick={() => count.value++}>Increment</button>
      <p>Count: {count.value}</p>
      <p>Double Count: {doubleCount.value}</p>
    </div>
  );
});
```

## Only one return per component

Because a component runs only once, a conditional return can't work as it does in React.

```js
import { $unison, ref } from "@unisonjs/vue";

const ConditionalComponent = $unison(() => {
  const count = ref(0);

  // Will not be evaluate more than once
  if (count.value > 0) {
    return <p>This is an early return.</p>;
  }

  return () => (
    <div>
      <button onClick={() => count.value++}>Increment</button>
      <p>Count: {count.value}</p>
    </div>
  );
});
```


## Must return a function and not the JSX itself

A component body runs only once so to enable the reflection of the state on UI, the component has to return a function to render on every state changes

## Can’t destructure props to preserve reactivity

State is accessed via a proxy, enabling the tracking of state and defining effect dependencies. To preserve reactivity, props should not be destructured.

```js
import { $unison, ref, watchEffect } from "@unisonjs/vue";

const ExampleComponent = $unison((props) => {
  const { value } = props; // Do not destructure props

  watchEffect(() => {
    console.log('props value is :', value); // [!code --]
    console.log('props value is :', props.value); // [!code ++]
  })

  return () => (
    <div>
      <p>Prop Value: {value}</p> // [!code --]
      <p>Prop Value: {props.value}</p> // [!code ++]
    </div>
  );
});
```
