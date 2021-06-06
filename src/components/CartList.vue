<template>
  <Loading v-model:active="isLoading" :is-full-page="fullPage"/>
  <section class="container py-5">
    <h2>購物車</h2>
    <div v-if="carts.length" class="text-end my-3">
      <button type="button" class="btn btn-danger" @click="removeAllCarts">清空購物車</button>
    </div>
    <div v-if="!carts.length">
      <h3 class="py-5 text-center">請新增商品!</h3>
    </div>
    <table v-else class="table">
      <thead>
        <tr>
          <th width="150" scope="col">圖片</th>
          <th width="30%" class="ps-5" scope="col">商品名稱</th>
          <th scope="col">數量</th>
          <th scope="col">售價</th>
          <th scope="col">合計</th>
          <th width="80" scope="col">編輯</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(product, key) in carts" :key="product.id">
          <td>
            <img class="img-fluid td-img" :src="product.product.imageUrl"
            :alt="product.product.title">
          </td>
          <td class="ps-5">{{ product.product.title }}</td>
          <td>
            <input type="num" class="text-center"
              @change="editQuntity(product, key)" v-model.number="product.qty">
            <span class="text-danger ms-3">庫存: {{ product.product.num }} {{ product.product.unit }}
            </span>
          </td>
          <td>{{ product.product.price }} 元</td>
          <td>{{ product.final_total }} 元</td>
          <td>
            <button type="button" class="btn btn-danger" @click="removeProduct(product.id)">
              刪除
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <div class="text-end">
      <p class="h3">總價: {{ total || 0 }} 元</p>
    </div>
  </section>
</template>

<script>
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  components: {
    Loading,
  },
  data() {
    return {
      isLoading: false,
      backupCarts: [],
      carts: [],
      total: 0,
      tempCartNum: [],
    };
  },
  methods: {
    getCarts() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`;
      this.axios(api)
        .then((res) => {
          this.isLoading = false;
          this.backupCarts = JSON.parse(JSON.stringify(res.data.data.carts));
          this.carts = res.data.data.carts;
          this.total = res.data.data.total;
          this.$emitter.emit('checkCartStatus', this.carts.length);
        })
        .catch((err) => {
          console.dir(err);
        });
    },
    removeProduct(id) {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${id}`;
      this.axios.delete(api)
        .then((res) => {
          this.isLoading = false;
          if (res.data.success) {
            this.$emitter.emit('addMsg', '成功移除商品');
            this.getCarts();
          } else {
            this.$emitter.emit('addMsg', '操作失敗');
          }
        })
        .catch((err) => {
          console.dir(err);
        });
    },
    removeAllCarts() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/carts`;
      this.axios.delete(api)
        .then((res) => {
          this.isLoading = false;
          if (res.data.success) {
            this.getCarts();
            this.$emitter.emit('addMsg', '成功移除商品');
          } else {
            this.$emitter.emit('addMsg', '操作失敗');
          }
        })
        .catch((err) => {
          console.dir(err);
        });
    },
    editQuntity(product, key) {
      if (product.qty <= 0 || product.qty > product.product.num) {
        this.carts[key].qty = this.backupCarts[key].qty;
        this.$emitter.emit('addMsg', '數量錯誤!');
        return;
      }
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${product.id}`;
      const data = {
        product_id: product.product_id,
        qty: product.qty,
      };
      this.axios.put(api, { data })
        .then((res) => {
          this.isLoading = false;
          if (res.data.success) {
            this.$emitter.emit('addMsg', '成功修改數量');
            this.getCarts();
          } else {
            this.$emitter.emit('addMsg', '操作失敗');
          }
        })
        .catch((err) => {
          console.dir(err);
        });
    },
  },
  created() {
    this.$emitter.on('getCarts', () => {
      this.getCarts();
    });
  },
  mounted() {
    this.getCarts();
  },
};
</script>

<style lang="scss" scoped>
td {
  line-height: 84px;
  > input {
    padding: 10px;
    width: 100px;
    height: 35px;
  }
  > img {
    height: 84px;
  }
}
</style>
