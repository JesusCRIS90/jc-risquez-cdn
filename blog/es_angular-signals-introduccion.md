
# Introducción a Angular Signals

Angular Signals es una de las incorporaciones más importantes al framework en los últimos años. Su objetivo principal es simplificar la gestión del estado y mejorar el rendimiento de las aplicaciones, ofreciendo un modelo reactivo más predecible y eficiente.


## ¿Qué es un Signal?

Un **Signal** es una primitiva reactiva que representa un valor que puede cambiar con el tiempo. Angular es capaz de rastrear automáticamente qué partes de la aplicación dependen de ese valor y reaccionar únicamente cuando es necesario.

A diferencia de otros enfoques reactivos, los Signals:
- Son síncronos
- No requieren suscripciones manuales
- Reducen el número de renders innecesarios

## Creando un Signal básico

Un ejemplo simple de un Signal en Angular sería:

```ts
import { signal } from '@angular/core';

const counter = signal(0);

counter.set(1);
counter.update(value => value + 1);
````

Para acceder al valor actual del Signal:

```ts
console.log(counter());
```

---

## Computed Signals

Los **Computed Signals** permiten derivar valores a partir de otros Signals.

```ts
import { signal, computed } from '@angular/core';

const counter = signal(2);
const doubleCounter = computed(() => counter() * 2);
```

Cuando `counter` cambia, `doubleCounter` se recalcula automáticamente.

---

## Signals y efectos

Los **effects** permiten ejecutar lógica secundaria cuando uno o más Signals cambian.

```ts
import { effect, signal } from '@angular/core';

const counter = signal(0);

effect(() => {
  console.log('El contador ha cambiado:', counter());
});
```

Los efectos son ideales para:

* Logging
* Sincronización con APIs externas
* Interacciones con el DOM

## Impacto en el rendimiento

Angular Signals mejoran el rendimiento al:

* Reducir el uso excesivo de Change Detection
* Evitar renders innecesarios
* Hacer explícitas las dependencias reactivas

Esto resulta especialmente útil en aplicaciones grandes o con interfaces complejas.


# Conclusión

Angular Signals introducen un modelo reactivo más claro, eficiente y fácil de mantener. Aunque no reemplazan completamente a RxJS, son una excelente herramienta para gestionar estado local y lógica derivada en componentes Angular modernos.
