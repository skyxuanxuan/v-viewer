# v-viewer

Image viewer component for vue, supports rotation, scale, zoom and so on, based on [viewer.js](https://github.com/fengyuanchen/viewerjs)

[![npm version](https://img.shields.io/npm/v/v-viewer.svg)](https://www.npmjs.com/package/v-viewer)
[![language](https://img.shields.io/badge/language-Vue2-brightgreen.svg)](https://www.npmjs.com/package/v-viewer)

[![npm version](https://img.shields.io/npm/v/v-viewer/next.svg)](https://www.npmjs.com/package/v-viewer)
[![language](https://img.shields.io/badge/language-Vue3-brightgreen.svg)](https://www.npmjs.com/package/v-viewer)

[![npm download](https://img.shields.io/npm/dw/v-viewer.svg)](https://www.npmjs.com/package/v-viewer)
[![license](https://img.shields.io/badge/license-MIT-brightgreen.svg)](https://mit-license.org/) 

## [v-viewer for vue2](https://github.com/mirari/v-viewer)

## [Live demo](https://mirari.github.io/vue3-viewer/)

## Quick Example

- [directive](https://codepen.io/mirari/pen/yLMPPWy)
- [component](https://codepen.io/mirari/pen/ZEeaaWZ)
- [api](https://codepen.io/mirari/pen/qBrVpNV)
- [thumbnail & source](https://codepen.io/mirari/pen/Vwpryax)
- [viewer callback](https://codepen.io/mirari/pen/eYveypz)
- [filter images](https://codepen.io/mirari/pen/mdWqpwa)
- [change images](https://codepen.io/mirari/pen/ExWbovw)

## [中文文档](https://mirari.cc/2021/05/31/Vue3%E5%9B%BE%E7%89%87%E6%B5%8F%E8%A7%88%E7%BB%84%E4%BB%B6v-viewer%EF%BC%8C%E6%94%AF%E6%8C%81%E6%97%8B%E8%BD%AC%E3%80%81%E7%BC%A9%E6%94%BE%E3%80%81%E7%BF%BB%E8%BD%AC%E7%AD%89%E6%93%8D%E4%BD%9C/)

## Installation

Install from NPM

```bash
npm install v-viewer@next
```

## Usage

To use `v-viewer`, simply import it and the `css` file, and call `app.use()` to install.

The component, directive and api will be installed together in the global.

```ts
import { createApp } from 'vue'
import 'viewerjs/dist/viewer.css'
import VueViewer from 'v-viewer'
import App from './App.vue'

export const app = createApp(App)
app.use(VueViewer, {
  debug: true,
})
app.mount('#app')

```
```html
<template>
  <div id="app">
    <!-- directive -->
    <div class="images" v-viewer>
      <img src="1.jpg">
      <img src="2.jpg">
      ...
    </div>
    <!-- component -->
    <viewer :images="images">
      <img v-for="src in images" :src="src" :key="src">
    </viewer>
    <!-- api -->
    <button type="button" @click="show">Click to show</button>
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue'
  export default defineComponent({
    name: 'App',
    data() {
      images: ['1.jpg', '2.jpg']
    },
    methods: {
      show() {
        this.$viewerApi({
          images: this.images,
        })
      },
    },
  })
</script>
```

### Support UMD

#### Browser

```html
<link href="//path/viewer.css" rel="stylesheet">
<script src="//path/vue.js"></script>
<script src="//path/viewer.js"></script>
<script src="//path/v-viewer.js"></script>
...
<script>
  Vue.use(VueViewer.default)
</script>
```

#### CommonJS

```javascript
var VueViewer = require('VueViewer')
```

#### AMD

```javascript
require(['VueViewer'], function (VueViewer) {});
```

### Usage of directive

Just add the directive `v-viewer` to any element, then all `img` elements in it will be handled by `viewer`.

You can set the options like this: `v-viewer="{inline: true}"`

Get the element by selector and then use `el.$viewer` to get the `viewer` instance if you need.

```html
<template>
  <div id="app">
    <div class="images" v-viewer="{movable: false}">
      <img v-for="src in images" :src="src" :key="src">
    </div>
    <button type="button" @click="show">Show</button>
  </div>
</template>
<script lang="ts">
  import 'viewerjs/dist/viewer.css'
  import { directive as viewer } from "v-viewer"
  export default defineComponent({
    directives: {
      viewer: viewer({
        debug: true,
      }),
    },
    data() {
      images: ['1.jpg', '2.jpg']
    },
    methods: {
      show () {
        const viewer = this.$el.querySelector('.images').$viewer
        viewer.show()
      }
    }
  })
</script>
```

#### Directive modifiers

##### static

The `viewer` instance will be created only once after the directive binded.

If you're sure the images inside this element won't change again, use it to avoid unnecessary re-render.

```
<div class="images" v-viewer.static="{inline: true}">
  <img v-for="src in images" :src="src" :key="src">
</div>
```

##### rebuild

The `viewer` instance will be updated by `update` method when the source images changed (added, removed or sorted) by default.

If you encounter any display problems, try rebuilding instead of updating.

```
<div class="images" v-viewer.rebuild="{inline: true}">
  <img v-for="src in images" :src="src" :key="src">
</div>
```

### Usage of component

You can simply import the component and register it locally too.

```html
<template>
  <div id="app">
    <viewer :options="options" :images="images"
            @inited="inited"
            class="viewer" ref="viewer"
    >
      <template #default="scope">
        <img v-for="src in scope.images" :src="src" :key="src">
        {{scope.options}}
      </template>
    </viewer>
    <button type="button" @click="show">Show</button>
  </div>
</template>
<script lang="ts">
  import 'viewerjs/dist/viewer.css'
  import { component as Viewer } from "v-viewer"
  export default defineComponent({
    components: {
      Viewer,
    },
    data() {
      images: ['1.jpg', '2.jpg']
    },
    methods: {
      inited (viewer) {
        this.$viewer = viewer
      },
      show () {
        this.$viewer.show()
      }
    }
  })
</script>
```

#### Component props

##### images

- Type: `Array`

##### trigger

- Type: `Array`

You can replace `images` with `trigger`, to accept any type of prop.
when the `trigger` changes, the component will re-render the viewer.

```html
<viewer :trigger="externallyGeneratedHtmlWithImages">
  <div v-html="externallyGeneratedHtmlWithImages"/>
</viewer>
```

##### rebuild

- Type: `Boolean`
- Default: `false`

The viewer instance will be updated by `update` method when the source images changed (added, removed or sorted) by default.

If you encounter any display problems, try rebuilding instead of updating.

```html
<viewer
  ref="viewer"
  :options="options"
  :images="images"
  rebuild
  class="viewer"
  @inited="inited"
>
  <template #default="scope">
    <img v-for="src in scope.images" :src="src" :key="src">
    {{scope.options}}
  </template>
</viewer>
```

#### Component events

##### inited

- viewer: `Viewer`

Listen for the `inited` event to get the `viewer` instance, or use `this.refs.xxx.$viewer`.

### Usage of api

> Only available in modal mode.

You can call the function: `this.$viewerApi({options: {}, images: []})` to show gallery without rendering the `img` elements yourself.

The function returns the current viewer instance.

```html
<template>
  <div id="app">
    <button type="button" class="button" @click="previewURL">URL Array</button>
    <button type="button" class="button" @click="previewImgObject">Img-Object Array</button>
  </div>
</template>
<script lang="ts">
  import 'viewerjs/dist/viewer.css'
  import { api as viewerApi } from "v-viewer"
  export default defineComponent({
    data() {
      sourceImageURLs: ['1.png', '2.png'],
      sourceImageObjects: [{'src':'thumbnail.png', 'data-source':'source.png'}]
    },
    methods: {
      previewURL () {
        // If you use the `app.use` full installation, you can use `this.$viewerApi` directly like this
        const $viewer = this.$viewerApi({
          images: this.sourceImageURLs
        })
      },
      previewImgObject () {
        // Or you can just import the api method and call it.
        const $viewer = viewerApi({
          options: {
            toolbar: true,
            url: 'data-source',
            initialViewIndex: 2
          },
          images: this.sourceImageObjects
        })
      }
    }
  })
</script>
```

## Options & Methods of Viewer

Refer to [viewer.js](https://github.com/fengyuanchen/viewerjs).

## Plugin options

### name

- Type: `String`
- Default: `viewer`

If you need to avoid name conflict, you can import it like this:
```ts
import { createApp } from 'vue'
import 'viewerjs/dist/viewer.css'
import VueViewer from 'v-viewer'
import App from './App.vue'

export const app = createApp(App)
app.use(VueViewer, {
  name: 'vuer',
  debug: true,
})
app.mount('#app')

```

```html
<template>
  <div id="app">
    <!-- directive name -->
    <div class="images" v-vuer="{movable: false}">
      <img v-for="src in images" :src="src" :key="src">
    </div>
    <button type="button" @click="show">Show</button>
    <!-- component name -->
    <vuer :images="images">
      <img v-for="src in images" :src="src" :key="src">
    </vuer>
  </div>
</template>
<script lang="ts">
  import { defineComponent } from 'vue'
  export default defineComponent({
    data() {
      images: ['1.jpg', '2.jpg']
    },
    methods: {
      show () {
        // viewerjs instance name
        const vuer = this.$el.querySelector('.images').$vuer
        vuer.show()
        // api name
        this.$vuerApi({
          images: this.images
        })
      }
    }
  })
</script>
```

### defaultOptions

- Type: `Object`
- Default: `undefined`

If you need to set the viewer default options, you can import it like this:

```ts
import { createApp } from 'vue'
import 'viewerjs/dist/viewer.css'
import VueViewer from 'v-viewer'
import App from './App.vue'

export const app = createApp(App)
app.use(VueViewer, {
  defaultOptions: {
    zIndex: 9999
  }
})
app.mount('#app')
```

And you can reset the default options at any other time:

```javascript
import VueViewer from 'v-viewer'

VueViewer.setDefaults({
  zIndexInline: 2021,
})
```
