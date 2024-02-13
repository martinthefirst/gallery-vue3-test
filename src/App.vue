<script setup lang="ts">
import TheGallery from "@/components/TheGallery.vue";
import type { GalleryItem } from "@/types/gallery";
import { onMounted, ref } from "vue";

const galleryData = ref<GalleryItem[]>([]);

onMounted(async () => {
  try {
    const response = await fetch("https://s3.eu-west-2.amazonaws.com/assets-test.fast-thinking.co.uk/recruitment/data.json");
    galleryData.value = await response.json();
  } catch (error) {
    console.error("Error fetching gallery data:", error);
  }
});
</script>

<template>
  <main>
    <h1>Simple Gallery</h1>
    <div v-if="galleryData.length">
      <TheGallery :galleryData="galleryData" />
    </div>
    <div v-else>
      <p>Loading gallery...</p>
    </div>
  </main>
</template>

<style lang="scss" scoped>
main {
  max-width: 1366px;
  padding: 15px;
  margin: 30px auto;
}
</style>