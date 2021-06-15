<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination">
      <li class="page-item"
          :class="{'disabled': pages.current_page === 1}">
        <a
          class="page-link"
          href="#"
          aria-label="Previous"
          @click.prevent="this.$emit('changePage', -1)"
        >
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>
      <li
        v-for="page in pages.total_pages"
        :key="page"
        class="page-item"
        :class="{ active: pages.current_page === page }"
      >
        <a
          class="page-link"
          href="#"
          @click.prevent="this.$emit('changePage', { page })"
          >{{ page }}</a
        >
      </li>
      <li class="page-item"
          :class="{'disabled': pages.current_page === pages.total_pages}">
        <a
          class="page-link"
          href="#"
          aria-label="Next"
          @click.prevent="this.$emit('changePage', 1)"
        >
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>
</template>

<script>
export default {
  props: {
    pages: {
      type: Object,
      default: () => {},
    },
  },
  emits: {
    changePage: (page) => {
      if (typeof page === 'number' || typeof page.page === 'number') return true;
      return false;
    },
  },
};
</script>
