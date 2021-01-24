<template>
  <div id="app" :loading="loading">
    <h3 v-if="loading">Loading ....</h3>
    <div v-if="!image">
      <input type="file" @change="onFileChange" />
    </div>
    <div
      v-else
      style="display: flex; flex-direction: column; justify-content: center; align-items:  center;"
    >
      <div
        style="display: flex; flex-direction:row; justify-content: center; align-items:  center;"
      >
        <img :src="image" style="width: 600px; margin-bottom: 10px;" />
        <h3>{{ text }}</h3>
      </div>
      <div
        style="display: flex; flex-direction: row;justify-content: justify-between;"
      >
        <button @click="recognizeImage" style="width: 100px">
          Recognize Image
        </button>
        <button @click="removeImage" style="width: 100px">Remove Image</button>
      </div>
    </div>
  </div>
</template>

<script>
import { createWorker } from "tesseract.js";
const worker = createWorker({
  logger: m => m
});

export default {
  name: "App",
  data() {
    return {
      image: "",
      text: "",
      loading: false
    };
  },
  methods: {
    async recognizeImage() {
      const image = this.image;
      this.loading = true;
      await worker.load();
      await worker.loadLanguage("eng");
      await worker.initialize("eng");
      const {
        data: { text }
      } = await worker.recognize(image);
      this.text = text;
      this.loading = false;
    },
    onFileChange(e) {
      let files = e.target.files || e.dataTransfer.files;
      this.createImage(files[0]);
    },
    createImage(file) {
      const reader = new FileReader();
      reader.onload = e => {
        this.image = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    removeImage() {
      this.image = "";
      this.text = "";
    }
  }
};
</script>

<style>
#app {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 20px;
}
</style>
