# Angular Signals - Introducción

## ¿Qué son los Signals en Angular?

En Angular, los Signals son una primitiva reactiva utilizada para gestionar el estado y propagar automáticamente los cambios a través de la aplicación.

Un Signal es esencialmente un contenedor alrededor de un valor que:
* Rastrea dónde se utiliza
* Notifica a sus dependencias cuando cambia
* Dispara actualizaciones automáticamente (sin suscripciones manuales)

Características clave:
* Reactivo: las actualizaciones fluyen automáticamente
* Sincrónico: no se necesita manejar suscripciones asíncronas
* Granular: solo se actualizan las partes afectadas de la UI
* Con seguimiento de dependencias: Angular sabe exactamente qué depende de qué

Ejemplo:

```ts
import { signal } from '@angular/core';

const count = signal(0);

count.set(1);           // actualizar valor
console.log(count());   // leer valor
```

## ¿Por qué se crearon los Signals?

Los Signals fueron introducidos para resolver varios desafíos históricos en el modelo de reactividad de Angular:

1. Limitaciones de rendimiento

El sistema tradicional dependía en gran medida de la detección de cambios basada en zonas, lo que:

* Revisaba grandes partes del árbol de componentes
* No era muy eficiente para aplicaciones grandes

Los Signals permiten actualizaciones granulares, lo que significa que solo se actualizan las partes exactas de la UI que dependen de un valor.

---

2. Complejidad de RxJS para estados simples

Angular previamente se apoyaba en RxJS para la reactividad.

Aunque potente, RxJS:

* Tiene una curva de aprendizaje pronunciada
* Requiere suscripciones y limpieza manual
* Suele ser excesivo para estados simples

Los Signals ofrecen una alternativa más simple para estado local y a nivel de componente.

---

3. Mejor experiencia de desarrollador

Los Signals buscan:

* Reducir código repetitivo (boilerplate)
* Eliminar suscripciones manuales
* Hacer el código más predecible y legible

---

4. Alineación con frameworks modernos

Otros frameworks como Solid, Vue y React han evolucionado hacia reactividad granular.

Angular Signals alinean Angular con estos paradigmas modernos.


## ¿Cuál era el sistema anterior?

Antes de los Signals, Angular se apoyaba en dos mecanismos principales:

1. Zone.js + Detección de cambios

Angular usaba Zone.js para detectar operaciones asíncronas y disparar la detección de cambios.

* Cada evento async (click, petición HTTP, temporizador) activaba una verificación global
* Angular recorría los componentes y actualizaba bindings

Problemas:
* Ineficiente para aplicaciones grandes
* Difícil de optimizar sin estrategias avanzadas (ej: OnPush)
* Poco explícito

---

2. Reactividad basada en RxJS

Para programación reactiva, Angular utilizaba:

* Observables
* Subjects
* Operadores

Ejemplo:

```ts
this.count$.subscribe(value => {
  // reaccionar a cambios
});
```

Problemas:
* Requiere suscripción/desuscripción manual
* Modelo mental más complejo
* No está fuertemente integrado con el renderizado de templates

## Comparativa - Signals vs RxJS vs Zone.js

| Característica                | Signals                      | RxJS (Observables)                       | Zone.js + Change Detection           |
| ----------------------------- | ---------------------------- | ---------------------------------------- | ------------------------------------ |
| **Concepto**                  | Estado reactivo granular     | Programación reactiva basada en streams  | Mecanismo global de detección        |
| **Uso principal**             | Estado local/componente      | Flujos async, eventos                    | Actualización de UI por tareas async |
| **Tipo de reactividad**       | Sincrónica, pull-based       | Asíncrona, push-based                    | Verificación global                  |
| **Boilerplate**               | Muy bajo                     | Medio–alto                               | Oculto pero complejo                 |
| **Suscripciones necesarias**  | No                           | Sí                                       | No (automático)                      |
| **Rendimiento**               | Alto (actualizaciones finas) | Alto (depende del uso)                   | Menor (revisa grandes árboles)       |
| **Curva de aprendizaje**      | Baja                         | Alta                                     | Media                                |
| **Integración con templates** | Nativa y directa             | Requiere `async` pipe o subscribe manual | Nativa                               |
| **Seguimiento de cambios**    | Automático                   | Manual mediante operadores               | Implícito vía zonas                  |


En resumen, los Signals reducen la necesidad de usar intensivamente RxJS y de depender de Zone.js.

* **Signals**: Mejores para gestión de estado y reactividad de UI
* **RxJS**: Mejores para flujos async complejos (HTTP, streams, eventos)
* **Zone.js**: Mecanismo legado para disparar actualizaciones globales


## Ejemplo real: Contador (3 enfoques)

### Usando Signals (Angular moderno)

```ts
import { signal, computed } from '@angular/core';

const count = signal(0);
const double = computed(() => count() * 2);

function increment() {
  count.update(v => v + 1);
}
```
Template:
```html
<button (click)="increment()">+</button>
<p>{{ count() }}</p>
<p>{{ double() }}</p>
```

* Sin suscripciones
* Actualizaciones automáticas
* Código mínimo

---

### Usando RxJS

```ts
import { BehaviorSubject, map } from 'rxjs';

count$ = new BehaviorSubject(0);
double$ = this.count$.pipe(map(v => v * 2));

increment() {
  this.count$.next(this.count$.value + 1);
}
```
Template:
```html
<button (click)="increment()">+</button>
<p>{{ count$ | async }}</p>
<p>{{ double$ | async }}</p>
```

* Requiere async pipe
* Más boilerplate
* Mayor carga mental (streams, operadores)

---

### Usando Zone.js + detección tradicional

```ts
count = 0;

increment() {
  this.count++;
}
```
Template:
```html
<button (click)="increment()">+</button>
<p>{{ count }}</p>
```

Angular (a través de Zone.js) detecta el click y vuelve a renderizar

* Puede revisarse todo el árbol de componentes
* Menor control sobre las actualizaciones


## ¿Cuándo se introdujeron los Signals en Angular?

Los Signals fueron introducidos en Angular v16 (mayo de 2023) como una funcionalidad en vista previa para desarrolladores.

* En Angular 16 → eran experimentales
* En versiones posteriores (v17+) → se volvieron estables y listos para producción

Este despliegue gradual permitió al equipo de Angular mejorar:

* APIs (signal, computed, effect)
* Integración con detección de cambios
* Interoperabilidad con herramientas existentes como RxJS


## ¿Qué beneficios aportan los Signals?

Los Signals aportan mejoras tanto en rendimiento como en experiencia de desarrollo:

1. Reactividad granular
* Solo se actualizan las partes de la UI que dependen del signal
* Evita renderizados innecesarios

2. Modelo mental más simple
* Sin streams, pipes o suscripciones
* Solo leer y escribir valores

3. Mejor rendimiento
* Elimina ciclos globales de detección de cambios
* Funciona eficientemente incluso en aplicaciones grandes

4. Seguimiento automático de dependencias
* Angular rastrea relaciones entre signals automáticamente
* No requiere configuración manual

5. Depuración más sencilla
* Estado explícito y sincrónico
* Sin flujos async ocultos

6. Futuro sin Zone.js
* Reduce dependencia de Zone.js
* Permite renderizado más predecible


## Pros y Contras de Angular Signals

| Pros                                                               | Contras                                                                     |
| ------------------------------------------------------------------ | --------------------------------------------------------------------------- |
| **Reactividad granular** → Solo actualiza lo necesario             | **No reemplaza completamente RxJS** → Sigue siendo útil para async complejo |
| **Mejor rendimiento** → Evita revisar todo el árbol de componentes | **Ecosistema en adaptación** → Algunas librerías aún no soportan signals    |
| **Código más simple** → Sin suscripciones ni `async` pipe          | **Curva de aprendizaje** → Nuevo modelo mental para devs Angular            |
| **Sincrónico y predecible** → Más fácil de depurar                 | **Riesgo de mal uso** → Abusar de signals puede empeorar la arquitectura    |
| **Dependencias automáticas** → Sin wiring manual                   | **Limitado para streams async** → No ideal para lógica basada en eventos    |
| **Templates más limpios** → Uso directo como `count()`             | **Coste de interoperabilidad** → Necesarios puentes con Observables         |
