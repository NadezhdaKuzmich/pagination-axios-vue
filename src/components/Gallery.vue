<template>
  <section v-if="!loading" class="gallery-section">
    <h1>Gallery</h1>
    <ul class="gallery-list">
      <li v-for="photo in photos" :key="photo.id" class="gallery-item">
        <img :src="photo.thumbnailUrl" :alt="photo.title" />
        <div class="photo-text">
          <p>{{ photo.title }}</p>
        </div>
      </li>
    </ul>
  </section>
  <Loader v-else />
</template>

<script>
import { defineComponent, ref, watchEffect } from "vue";
import Loader from "./Loader.vue";
import { fetchPhotos } from "@/api/fetchData";

export default defineComponent({
  components: { Loader },
  props: {
    page: {
      type: Number,
      required: true,
    },
    limit: {
      type: Number,
      required: true,
    },
  },
  setup(props) {
    const photos = ref([]);
    const loading = ref(true);

    const loadPhotos = async () => {
      photos.value = await fetchPhotos(props.page, props.limit);
      loading.value = false;
    };

    watchEffect(() => {
      loadPhotos();
    });

    return {
      photos,
      loading,
    };
  },
});
</script>

<style scoped>
.gallery-section {
  text-align: center;
  padding: 20px 0 0;
}
.gallery-list {
  display: flex;
  flex-wrap: wrap;
  align-items: baseline;
  justify-content: center;
  gap: 20px;
  padding: 20px 0;
}

.gallery-item {
  display: grid;
  width: 100%;
  min-width: 180px;
  width: calc(100% / 5 - 16px);
  justify-items: center;
  align-items: center;
  border-radius: 8px;
  overflow-y: scroll;
}

.photo-text {
  width: 100%;
  min-height: 40px;
  background: #fdf5f51a;
  border-radius: 8px;
  padding: 10px 0;
  margin-top: 10px;
}

.photo-text p {
  max-width: 140px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  margin: auto;
}
</style>