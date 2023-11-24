<template>
  <main>
    <div class="container" v-if="!loading">
      <Gallery :limit="perPage" :page="currentPage" />
      <Pagination
        :on-change-page="handleChangePage"
        :current-page="currentPage"
        :page-count="pageCount" />
    </div>
    <Loader v-else />
  </main>
</template>

<script>
import { defineComponent, onMounted, ref, computed } from "vue";
import Loader from "./components/Loader.vue";
import { fetchAllPhotos } from "./api/fetchData";
import Gallery from "./components/Gallery.vue";
import Pagination from "./components/Pagination.vue";

export default defineComponent({
  name: "App",
  components: {
    Loader,
    Gallery,
    Pagination,
  },
  setup() {
    const loading = ref(true);
    const currentPage = ref(1);
    const perPage = ref(15);
    const photosCount = ref(0);

    const pageCount = computed(() => {
      return Math.ceil(photosCount.value / perPage.value);
    });

    const handleChangePage = (page) => {
      currentPage.value = page;
    };

    onMounted(() => {
      fetchAllPhotos()
        .then((data) => {
          photosCount.value = data.length;
          loading.value = false;
        })
        .catch((error) => {
          console.error(error);
          loading.value = false;
        });
    });

    return {
      loading,
      currentPage,
      perPage,
      photosCount,
      pageCount,
      handleChangePage,
    };
  },
});
</script>

<style>
body {
  font-family: "Lexend", sans-serif;
  color: #e6e7fb;
  background: #1e1f2b;
  margin: 0;
}

img {
  width: 100%;
  border-radius: 8px;
}

h1 {
  font-size: 28px;
}

.container {
  max-width: 1000px;
  padding: 0 20px;
  margin: auto;
}
</style>