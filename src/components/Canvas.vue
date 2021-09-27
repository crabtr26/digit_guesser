<template>
  <div class="flex p-2 justify-center bg-gray-700">
    <div class="flex-col p2 justify-center">
      <div class="flex py-4 justify-center text-white text-4xl">
        <h1 > Draw a Digit </h1>
      </div>
      <div class="flex justify-center">
        <Vue3Signature ref="canvas" :sigOption="options" :w="'120px'" :h="'120px'"></Vue3Signature>
      </div>     
      <div class="flex justify-center">
        <button class="p-2 text-white text-xl" @click="classify_image">Predict</button>
        <button class="p-2 text-white text-xl" @click="clear">Clear</button>
      </div>
      <div v-if="prediction" class="flex py-12 text-green-300 text-4xl">
          <h1> Prediction: {{ prediction }} </h1>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, inject } from 'vue'
import svm_byte_string from '../assets/digits_svm_byte_string.txt'

export default {
  setup() {

    const canvas = ref(null)
    const prediction = ref(null)

    const options = {
        penColor: "rgb(0,0,0)",
        backgroundColor: "rgb(255,255,255)"
    }

    const clear = () => {
      canvas.value.clear()
      prediction.value = null
    }

    const save = () => {
      canvas.value.save()
    }

    const pyodide = inject("pyodide")

    const classify_image = () => {
      pyodide.value.globals.set("image_string", canvas.value.save())
      pyodide.value.globals.set("svm_byte_string", svm_byte_string)
      pyodide.value.runPython(`
          # Split off the image byte-string from the total string
          base64_img = image_string.split(",")[-1]

          # Convert image byte-string to image array
          base64_img_bytes = base64_img.encode('utf-8')
          decoded_image_data = base64.decodebytes(base64_img_bytes)
          image = np.invert(np.array(imageio.imread(decoded_image_data)))

          # Convert image to grayscale
          gray_image = rgb2gray(image)

          # Image is 80px by 80px. Need to downsample to 8px by 8px to match MNIST data
          image_downscaled = downscale_local_mean(gray_image, (15, 15))

          # Reshape image to 1-D array
          x = image_downscaled.reshape(64,)

          # Standardize input vector
          std_x = (x - x.mean()) / x.std()

          # Predict, display
          svm = pickle.loads(eval(svm_byte_string))
          pred = svm.predict([std_x])
      `)
      prediction.value = pyodide.value.globals.get("pred").toJs()
    }

    return {
      canvas,
      prediction,
      options,
      clear,
      classify_image,
    }

  }
}
</script>