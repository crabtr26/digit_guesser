<template>
  <div id="app" class="bg-gray-700 h-screen">
    <Header />
    <Canvas v-if="pyodide" />
    <h1 v-else>  Loading... </h1>
  </div>
</template>

<script>
import Canvas from './components/Canvas.vue'
import Header from './components/Header.vue'
import { ref, onMounted, provide, computed } from 'vue'

export default {
  setup () {
    const pyodide = ref(null)
    onMounted(
      async () => {
        let load_pyodide = await loadPyodide({
          indexURL: "https://cdn.jsdelivr.net/pyodide/v0.18.1/full/"
        })
        await load_pyodide.loadPackage("scikit-image")
        await load_pyodide.loadPackage("scikit-learn")
        await load_pyodide.loadPackage("imageio")
        await load_pyodide.loadPackage("numpy")
        load_pyodide.runPython(`
            import pickle
            import base64
            import imageio
            import numpy as np
            from skimage.transform import downscale_local_mean
            from skimage.color import rgb2gray
        `)
        pyodide.value = load_pyodide
      }
    )
    provide("pyodide", computed(() => pyodide.value))
    return {pyodide}
  },
  components: {
    Header,
    Canvas
  },
}
</script>