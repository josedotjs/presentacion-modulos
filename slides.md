---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# Módulos en Nuxt

Cómo compartir código entre proyectos usando módulos.

<div class="pt-12">
  <span>José Daniel Penin Lovera</span>
</div>

<div class="abs-br m-6 flex gap-2">
  Beerjs Córdoba 61, 17/02/2022
</div>

---

# ¿Qué es Nuxt?

<div class="mt-6">
Es un framework de desarrollo basado en Vue
</div>

<div class="mt-10" />

- Permite SSR y SSG
- Enrutamiento basado en file-system
- Registro automático de componentes
- Facilita el trabajo con stores de vuex
- [Provee algunos componentes propios](https://nuxtjs.org/docs/features/nuxt-components/#the-client-only-component) (Nuxt NuxtLink NuxtChild client-only )
  <br>
  <br>

<div class="abs-bl flex gap-2 mb-6">
⚠ El código mostrado está basado en la versión 2 de Nuxt usando SSR
</div>

<!--
You can have `style` tag in markdown to override the style for the current page.
Learn more: https://sli.dev/guide/syntax#embedded-styles
-->

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# Módulos en Nuxt

Los módulos en Nuxt permiten extender la funcionalidad y facilitar integraciones

<div class="mt-10" />

- Son funciones que se ejecutan en orden secuencial cuando arranca Nuxt
- Dan acceso a la configuración y la instancia de Nuxt
- Permiten agregar plugins, páginas, componentes, etc.
- Exponen hooks propios
- "Es como tener una mini app dentro de nuestra app"

---

# Páginas

<div class="mt-10">
Son componentes de Vue a los que Nuxt les agrega funcionalidad
</div>
<div class="mt-10" />

- Tienen hooks propios para trabajar con SSR
- Se les puede definir un layout
- Se les puede indicar que middlewares ejecutar antes de renderizar la página

```js {3-8}
export default function (moduleOptions) {
  const { extendRoutes } = this
  extendRoutes((routes) => {
    routes.push({
      path: '/mypage',
      component: 'path/to/my/page',
    })
  })
}
```

<span>
⚠ Cómo se agregan las rutas es importante (push vs unshift)
</span>

---

# Plugins

<div class="my-10">
Son plugins de javascript que corren antes de instanciar la App de Vue.
</div>

#### Es el lugar adecuado para agregar plugins de Vue o inyectar funciones y constantes en el contexto de Nuxt

```js {3-12}
/// modulo/index.js
export default function (moduleOptions) {
  const { addPlugin, options } = this
  addPlugin({
    src: 'path/al/plugin',
    // mode: client opcional, también se puede reemplazar por convención
  })
  // ⚠ addPlugin agrega el plugin al array con unshift
  // otra opción es
  options.plugins.push({
    src: 'path/al/plugin',
  })
}
```

---

# Módulos

<div class="my-10">
También podemos agregar otros módulos, esto permite poder extraer funcionalidades reutilizables en módulos más chicos
</div>

- Podemos agregar los módulos en común que utilizan los proyectos

```js {4}
export default function (moduleOptions) {
  const { addModule } = this
  const opciones = {}
  addModule(['modulo o path', opciones])
}
```

---

# Server Middlewares

<div class="my-10">
Nuxt internamente crea una instancia de connect a la que podemos agregar nuestro middleware personalizado. Esto nos permite registrar rutas adicionales sin necesidad de un servidor externo.
</div>

```js {3-6}
export default function (moduleOptions) {
  const { addServerMiddleware } = this
  addServerMiddleware({
    handler: 'path/al/server-middleware',
    path: '/ruta',
  })
}
```

---

# Componentes

<div class="my-10">
Probablemente la parte más importante a compartir entre proyectos son los componentes.
Con los módulos podemos definir los componentes comunes y reutilizarlos desde los proyectos que lo consumen.
</div>

```js {3-8}
export default function (moduleOptions) {
  const { nuxt } = this
  nuxt.hook('components:dirs', (dirs) => {
    dirs.push({
      path: 'path/folder/components',
      // prefix: 'common',
    })
  })
}
```

<div class="abs-bl flex gap-2 mb-6">
⚠ Requiere que los proyectos que consumen el módulo tengan activado 
<code>
  components: true
</code> en <code>nuxt.config.js</code>
</div>
