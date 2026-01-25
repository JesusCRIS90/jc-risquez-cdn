# Introduction to Angular Signals

Angular Signals are one of the most significant additions to the framework in recent years. Their main goal is to simplify state management and improve application performance by providing a more predictable and efficient reactive model.

## What is a Signal?

A **Signal** is a reactive primitive that represents a value that can change over time. Angular automatically tracks which parts of the application depend on that value and updates only what is strictly necessary.

Compared to other reactive approaches, Signals:
- Are synchronous
- Do not require manual subscriptions
- Reduce unnecessary re-renders

## Creating a basic Signal

A simple example of a Signal in Angular:

```ts
import { signal } from '@angular/core';

const counter = signal(0);

counter.set(1);
counter.update(value => value + 1);
```

To read the current value of a Signal:

```ts
console.log(counter());
```


## Computed Signals

**Computed Signals** allow you to derive values from other Signals.

```ts
import { signal, computed } from '@angular/core';

const counter = signal(2);
const doubleCounter = computed(() => counter() * 2);
```

Whenever `counter` changes, `doubleCounter` is recalculated automatically.


## Signals and effects

**Effects** allow you to run side-effect logic when one or more Signals change.

```ts
import { effect, signal } from '@angular/core';

const counter = signal(0);

effect(() => {
  console.log('Counter changed:', counter());
});
```

Effects are ideal for:

* Logging
* Synchronizing with external APIs
* DOM interactions


## Performance impact

Angular Signals improve performance by:

* Reducing excessive Change Detection
* Preventing unnecessary renders
* Making reactive dependencies explicit

This is especially beneficial for large applications or complex UIs.

# Conclusion

Angular Signals introduce a clearer, more efficient, and maintainable reactive model. While they do not fully replace RxJS, they are an excellent tool for managing local state and derived logic in modern Angular components.