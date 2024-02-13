<script setup lang="ts">
import initialImage from "@/assets/no-image.png";
import type { GalleryItem } from "@/types/gallery";
import { ref } from "vue";

const props = defineProps<{
    galleryData: GalleryItem[];
}>();

const maxRetries = 10;
const processedGalleryItems = ref<GalleryItem[]>([]);

async function loadImage(item: GalleryItem) {
    let retries = 0;
    let delayTime = 2000; // Delay between retries in seconds

    const fetchImage = async () => {
        const response = await fetch(item.url);

        // Set fetched image
        if (response.ok) return item.url;

        // Retry & set alternative figure
        retries++;
        if (retries < maxRetries) {
            console.info(`Try ${retries} out of ${maxRetries}, unable to fetch image. Next try in ${delayTime / 1000} seconds.`, item.url);
            setTimeout(fetchImage, delayTime);
            delayTime *= 2;
        } else console.error(`Maximum ${maxRetries} retries reached. The image will not be loaded.`, item.url);

        return initialImage;
    };

    return await fetchImage();
}

async function initializeGallery() {
    for (const item of props.galleryData) {
        const imageUrl = await loadImage(item);
        processedGalleryItems.value.push({ ...item, url: imageUrl });
    }
}

initializeGallery();
</script>

<template>
    <div class="gallery">
        <div
            v-for="(item, index) in processedGalleryItems"
            :key="index"
            class="gallery-item"
        >
            <img
                :src="item.url"
                :alt="item.label"
            />
            <p class="gallery-label">{{ item.label }}</p>
        </div>
    </div>
</template>
  
<style lang="scss" scoped>
.gallery {
    display: grid;
    grid-template-columns: repeat(auto-fill, 160px);
    justify-items: center;
    gap: 12px;

    .gallery-item {
        width: 160px;

        img {
            width: inherit;
            height: 140px;
            object-fit: cover;
            border-radius: 4px;
        }

        .gallery-label {
            color: #000;
            font-family: serif;
            font-size: 12px;
            margin: 0;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
    }
}
</style>
  