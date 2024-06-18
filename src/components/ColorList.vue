<script>
import axios from 'axios'

export default {
  data() {
    //returns the data, colors, So so that we can print it in the UI
    return {
      colors: [],
      selectedColor: { name: '', hex: '', code: '' } // Object to store currently selected color
    }
  },
  mounted() {
    this.fetchColors()
  },
  computed: {
    textColor() {
      // Determine if the text should be black or white based on the background color
      return this.getLuminance(this.selectedColor.hex) > 0.5 ? '#000000' : '#ffffff'
    }
  },
  methods: {
    async fetchColors() {
      try {
        const response = await axios.get('https://api.prolook.com/api/colors/prolook') //getting the data from the API
        this.colors = response.data.colors // Assuming response.data is an array of colors
        console.log(response.data.colors)
      } catch (error) {
        console.error('Error fetching colors:', error)
      }
    },
    previewColor(color) {
      // Update selectedColor object when a color is clicked
      this.selectedColor = { name: color.name, hex: color.hex_code, code: color.color_code }
      console.log(this.selectedColor)
    },
    getLuminance(hex) {
      const rgb = parseInt(hex.slice(1), 16)
      const r = (rgb >> 16) & 0xff
      const g = (rgb >> 8) & 0xff
      const b = (rgb >> 0) & 0xff
      return 0.2126 * (r / 255) + 0.7152 * (g / 255) + 0.0722 * (b / 255)
    }
  }
}
</script>

<template>
  <div>
    <div class="row">
      <div class="col-md-6">
        <h1>Colors:</h1>
        <div class="card-body table-responsive">
          <div class="list-group">
            <table class="table table-hover">
              <thead>
                <tr>
                  <th>Color</th>
                  <th>Action</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="color in colors" :key="color.id">
                  <td>{{ color.name }}</td>

                  <td>
                    <button @click="previewColor(color)" style="background-color: #008cba">
                      Preview
                    </button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <div class="col-md-6">
        <div class="card">
          <div class="card-header">Color Preview</div>
          <div class="color-preview" :style="{ backgroundColor: '#' + selectedColor.hex }">
            <div class="color-box" :style="{ color: textColor }">
              <h5 class="card-title">Name: {{ selectedColor.name }}</h5>
              <p class="card-text">Hex: {{ selectedColor.hex }}</p>
              <p class="card-text">Color Code: {{ selectedColor.code }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.table-responsive {
  max-height: 400px;
}
</style>
