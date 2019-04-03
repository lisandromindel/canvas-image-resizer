<template>
  <div id="app">
    <input type="file" ref="input" />
    <input type="number" ref="inputWidth" placeholder="Max Width: 800" />
    <input type="number" ref="inputHeight" placeholder="Max Height: 800" />
    <div class="image-wrapper">
      <img ref="result" />
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  data () {
    return {
      maxWidth: 800,
      maxHeight: 800
    }
  },
  mounted: function () {
    var input = this.$refs.input
    input.addEventListener('change', this.onInput)
  },
  methods: {
  	onInput: function (e) {
    	const input = this.$refs.input
      const file = input.files[0]
      const preview = new Image();
      const uploadedImage = window.URL.createObjectURL(file)
      
      preview.onload = () => {
        const imageWidth = preview.width
        const imageHeight = preview.height
        const isImageLandscape = imageWidth / imageHeight > 1

        this.reduceImage(uploadedImage, isImageLandscape)
      }

      preview.src = uploadedImage
    },
  	reduceImage: function (imageURL, landscape) {
      const canvas = document.createElement('canvas')
      const ctx = canvas.getContext("2d")
      const img = new Image()

      const maxWidth = parseInt(this.$refs.inputWidth.value) || this.maxWidth
      const maxHeight = parseInt(this.$refs.inputHeight.value) || this.maxHeight

      let scale = 1
      
      img.onload = () => {
        // set size proportional to image
        if (landscape) {
          scale = maxWidth / img.width
        } else {
          scale = maxHeight / img.height
        }

        scale = Math.round(scale * 10) / 10

        canvas.width = img.width
        canvas.height = img.height

        console.log(canvas.width, canvas.height, scale)

        // step 1 - resize to 50%
        var oc = document.createElement('canvas'),
            octx = oc.getContext('2d');

        oc.width = img.width * scale;
        oc.height = img.height * scale;

        octx.mozImageSmoothingEnabled = true;
        octx.imageSmoothingQuality = "high";
        octx.webkitImageSmoothingEnabled = true;
        octx.msImageSmoothingEnabled = true;
        octx.imageSmoothingEnabled = true;
        octx.drawImage(img, 0, 0, oc.width, oc.height);

        // step 3, resize to final size
        ctx.mozImageSmoothingEnabled = true;
        ctx.imageSmoothingQuality = "high";
        ctx.webkitImageSmoothingEnabled = true;
        ctx.msImageSmoothingEnabled = true;
        ctx.imageSmoothingEnabled = true;
        ctx.drawImage(oc, 0, 0, oc.width, oc.height, 0, 0, canvas.width, canvas.height);
                      
        this.exportCanvas(canvas, canvas.width * scale, canvas.height * scale)
      }
      
      img.src = imageURL;
    },
    exportCanvas(canvas, width, height) {
    	var dataURL = canvas.toDataURL()
      var result = this.$refs.result

      // result.width = width
      // result.height = height

      result.src = dataURL
    }
  }
}
</script>

<style>
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  padding-top: 60px;
  box-sizing: border-box;
}

.image-wrapper {
  width: 100%;
  padding: 30px;
  box-sizing: border-box;
}

img {
  width: 100%;
  height: auto;
}
</style>
