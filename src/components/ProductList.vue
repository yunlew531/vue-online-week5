<template>
  <Loading v-model:active="isLoading" :is-full-page="fullPage"/>
  <section class="container py-5">
    <h2>商品</h2>
    <div class="row">
      <div
        class="col-xl-4 col-sm-6 mb-5"
        v-for="product in products"
        :key="product.id"
      >
        <div class="card h-100">
          <img
            :src="product.imageUrl ? product.imageUrl : ''"
            class="card-photo card-img-top"
            :alt="product.title"
          />
          <div class="card-body d-flex flex-column justify-content-between">
            <h3 class="card-title h3">{{ product.title }}</h3>
            <p class="mb-2">庫存: {{ product.num }}</p>
            <p class="d-flex justify-content-between h4">
              <span>原價: {{ product.origin_price }}</span
              ><span>售價: {{ product.price }}</span>
            </p>
            <div class="d-flex justify-content-between">
              <a
                href="#"
                class="btn btn-outline-secondary"
                @click.prevent="getIntroduction(product.id)"
              >
                詳細資訊
              </a>
              <a href="#" class="btn btn-danger" @click.prevent="addCart(product.id)">加入購物車</a>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="d-flex justify-content-center">
      <Pagination :pages="pagination" @change-page="changePage" />
    </div>
  </section>
</template>

<script>
import Pagination from '@/components/Pagination.vue';
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  components: {
    Pagination,
    Loading,
  },
  data() {
    return {
      isLoading: false,
      products: {},
      pagination: {},
    };
  },
  methods: {
    getProducts(page = 1) {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products?page=${page}`;
      this.axios
        .get(api)
        .then((res) => {
          this.isLoading = false;
          this.products = res.data.products;
          this.pagination = res.data.pagination;
        })
        .catch((err) => {
          console.dir(err);
        });
    },
    changePage(page) {
      const finalPage = typeof page === 'object' ? page.page : this.pagination.current_page + page;
      if (finalPage === this.pagination.current_page) return;
      this.getProducts(finalPage);
    },
    getIntroduction(id) {
      this.$emitter.emit('getIntroduction', id);
    },
    addCart(id) {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`;
      const data = {
        product_id: id,
        qty: 1,
      };
      this.axios.post(api, { data })
        .then((res) => {
          this.isLoading = false;
          if (res.data.success) {
            this.$emitter.emit('addMsg', '成功加入購物車!');
            this.$emitter.emit('getCarts');
          } else {
            this.$emitter.emit('addMsg', '操作失敗!');
          }
        })
        .catch((err) => {
          console.dir(err);
        });
    },
  },
  mounted() {
    this.getProducts();
  },
};
</script>

<style lang="scss" scoped>
.card-photo {
  height: 225px;
}
</style>
