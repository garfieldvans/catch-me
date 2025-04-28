<template>
  <div id="app">
    <h1 class="text-[30px] font-bold mb-[20px]">Screenshot Capture</h1>
    <div class="screenshot flex bg-teal-500 justify-center items-center">
      <input
      class="border border-gray-300 rounded py-[6px] px-[20px] mr-[10px]"
      v-model="url"
      type="text"
      placeholder="Enter URL"
    />
    <button @click="handleCapture" :disabled="loading" class="bg-white py-[6px] px-[20px] rounded">
      {{ loading ? 'Capturing...' : 'Capture Screenshot' }}
    </button>
    </div>
    <div class="mt-[20px] bg-indigo-300 flex flex-col justify-center items-center">
      <p v-if="error" style="color: red;">{{ error }}</p>
      <img v-if="image" :src="image" alt="Screenshot" />
      <!-- Download button for the screenshot -->
      <button v-if="image" @click="downloadImage" class="mt-[10px] bg-blue-500 text-white py-[6px] px-[20px] rounded">
        Download Screenshot
      </button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      url: '',
      image: null,
      loading: false,
      error: null,
    };
  },
  methods: {
    async handleCapture() {
      this.loading = true;
      this.error = null;
      this.image = null;

      try {
        const response = await axios.post('http://localhost:3000/api/screenshot', {
          url: this.url,
        }, { responseType: 'arraybuffer' });

        const blob = new Blob([response.data], { type: 'image/png' });
        this.image = URL.createObjectURL(blob);
      } catch (err) {
        this.error = 'Failed to capture screenshot';
      } finally {
        this.loading = false;
      }
    },

    // Method to download the screenshot
    downloadImage() {
      const a = document.createElement('a');
      a.href = this.image;
      a.download = 'screenshot.png'; // The name of the downloaded file
      a.click();
    },
  },
};
</script>