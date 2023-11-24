<template>
  <div class="pagination">
    <button
      :disabled="currentPage === 1"
      @click="handleChangePage(currentPage - 1)">
      Previous
    </button>
    <button v-if="showFirstEllipsis" @click="handleChangePage(1)">1</button>
    <span v-if="showFirstEllipsis">...</span>
    <button
      v-for="pageNumber in visiblePageNumbers"
      :key="pageNumber"
      @click="handleChangePage(pageNumber)"
      :class="{ active: currentPage === pageNumber }">
      {{ pageNumber }}
    </button>
    <span v-if="showLastEllipsis" @click="handleChangePage(pageCount)"
      >...</span
    >
    <button v-if="showLastEllipsis" @click="handleChangePage(pageCount)">
      {{ pageCount }}
    </button>
    <button
      :disabled="currentPage === pageCount"
      @click="handleChangePage(currentPage + 1)">
      Next
    </button>
  </div>
</template>

<script>
import { defineComponent, computed, watch } from "vue";

export default defineComponent({
  props: {
    pageCount: {
      type: Number,
      required: true,
    },
    currentPage: {
      type: Number,
      required: true,
    },
    onChangePage: {
      type: Function,
      required: true,
    },
  },
  setup(props) {
    const visiblePageNumbers = computed(() => {
      const result = [];
      let startPage = Math.max(props.currentPage - 5, 1);
      let endPage = Math.min(startPage + 9, props.pageCount);
      if (endPage - startPage < 9) {
        startPage = Math.max(endPage - 9, 1);
      }

      for (let i = startPage; i <= endPage; i++) {
        result.push(i);
      }
      return result;
    });

    const showFirstEllipsis = computed(() => {
      return visiblePageNumbers.value[0] > 1;
    });

    const showLastEllipsis = computed(() => {
      return (
        visiblePageNumbers.value[visiblePageNumbers.value.length - 1] <
        props.pageCount
      );
    });

    const handleChangePage = (pageNumber) => {
      if (pageNumber < 1 || pageNumber > props.pageCount) {
        return;
      }
      props.onChangePage(pageNumber);
    };

    watch(
      () => props.currentPage,
      () => {
        if (visiblePageNumbers.value.indexOf(props.currentPage) === -1) {
          handleChangePage(props.currentPage);
        }
      }
    );

    return {
      visiblePageNumbers,
      showFirstEllipsis,
      showLastEllipsis,
      handleChangePage,
    };
  },
});
</script>

<style scoped>
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  align-content: flex-end;
  margin: 20px 0 60px;
  gap: 5px;
}

.pagination button {
  color: #e6e7fb;
  background: #272839;
  border: none;
  border-radius: 8px;
  padding: 4px 8px;
  cursor: pointer;
}

.pagination button:hover,
.pagination button.active {
  background: #3e3e4a;
}

button:disabled {
  opacity: 0.5;
}
</style>