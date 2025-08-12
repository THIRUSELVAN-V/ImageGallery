<template>
  <div class="gallery-container">
    <h2>🌿 Nature Gallery</h2>
    <div class="gallery">
      <div
        v-for="(image, index) in images"
        :key="index"
        class="gallery-item"
      >
        <img :src="image.src" :alt="image.alt" @click="selectImage(index)" />
        <button class="delete-btn" @click="deleteImage(index)">Delete</button>
      </div>
    </div>

    <div v-if="isLightboxActive" class="lightbox">
      <button @click="prevImage" class="nav-btn">⟨</button>
      <img
        :src="selectedImage.src"
        :alt="selectedImage.alt"
        @wheel="zoomImage"
        :style="{ transform: 'scale(' + zoom + ')' }"
      />
      <button @click="nextImage" class="nav-btn">⟩</button>
      <button @click="closeLightbox" class="close-btn">X</button>
      <div class="meta">
        <label>Title: <input v-model="selectedImage.title" /></label>
        <label>Description: <textarea v-model="selectedImage.description" /></label>
      </div>
    </div>

    <button
      v-if="deletedImages.length"
      class="restore-btn"
      @click="restoreImage"
    >
      Restore Last Deleted
    </button>
  </div>
</template>

<script>
export default {
  name: "ImageGallery",
  data() {
    return {
      images: Array.from({ length: 15 }, (_, i) => ({
  src: `https://picsum.photos/seed/nature${i + 1}/900/600`,
  alt: `Image ${i + 1}`,
  title: "",
  description: ""
})),
      deletedImages: [],
      selectedImageIndex: null,
      zoom: 1
    };
  },
  computed: {
    isLightboxActive() {
      return this.selectedImageIndex !== null;
    },
    selectedImage() {
      return this.images[this.selectedImageIndex];
    }
  },
  methods: {
    selectImage(index) {
      this.selectedImageIndex = index;
      this.zoom = 1;
    },
    closeLightbox() {
      this.selectedImageIndex = null;
    },
    deleteImage(index) {
      const removed = this.images.splice(index, 1)[0];
      this.deletedImages.push(removed);
      if (index === this.selectedImageIndex) this.closeLightbox();
    },
    restoreImage() {
      if (this.deletedImages.length) {
        this.images.push(this.deletedImages.pop());
      }
    },
    zoomImage(event) {
      this.zoom += event.deltaY > 0 ? -0.1 : 0.1;
      this.zoom = Math.max(0.5, Math.min(this.zoom, 2));
    },
    prevImage() {
      if (this.selectedImageIndex > 0) {
        this.selectedImageIndex--;
        this.zoom = 1;
      }
    },
    nextImage() {
      if (this.selectedImageIndex < this.images.length - 1) {
        this.selectedImageIndex++;
        this.zoom = 1;
      }
    }
  }
};
</script>

<style scoped>
.gallery-container {
  max-width: 100%;
  margin: auto;
}
.gallery {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 15px;
}
.gallery-item {
  position: relative;
  width: 300px; 
  transition: transform 0.3s;
}
.gallery-item:hover {
  transform: scale(1.05);
}
.gallery-item img {
  width: 100%;
  height: auto;
  cursor: pointer;
}
.delete-btn {
  position: absolute;
  top: 5px;
  right: 5px;
  background: crimson;
  color: white;
  border: none;
  padding: 5px;
  cursor: pointer;
}
.restore-btn {
  margin-top: 20px;
  background: #3498db;
  color: white;
  padding: 10px;
  border: none;
  cursor: pointer;
}
.lightbox {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.lightbox img {
  max-height: 70%;
  transition: transform 0.3s;
}
.nav-btn {
  background: transparent;
  color: white;
  font-size: 3rem;
  border: none;
  cursor: pointer;
}
.close-btn {
  position: absolute;
  top: 20px;
  right: 30px;
  background: red;
  color: white;
  padding: 10px;
  cursor: pointer;
}
.meta {
  background: white;
  padding: 10px;
  margin-top: 15px;
  width: 300px;
}
.meta input,
.meta textarea {
  width: 100%;
  margin-bottom: 10px;
}
</style>
