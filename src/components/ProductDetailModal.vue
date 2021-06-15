<template>
  <Loading v-model:active="isLoading" />
  <div class="modal fade" id="productDetailModal" tabindex="-1" aria-labelledby="exampleModalLabel"
    aria-hidden="true" ref="productDetailModal">
    <div class="modal-dialog modal-lg">
      <div class="modal-content">
        <div class="modal-header d-flex justify-content-between">
          <span></span>
          <h3 class="modal-title" id="exampleModalLabel">{{ tempProductDetail.title }}</h3>
          <button type="button" class="btn-close m-0" data-bs-dismiss="modal" aria-label="Close">
          </button>
        </div>
        <div class="modal-body">
          <div class="d-flex">
            <div class="bg-style w-50"
              :style="{'background-image': 'url('+ tempProductDetail.imageUrl +')'}"></div>
            <div class="w-50 p-3 d-flex flex-column">
              <div class=" flex-grow-1 ">
                <p>分類: {{ tempProductDetail.category }}</p>
                <p>{{ tempProductDetail.content }}</p>
                <p>{{ tempProductDetail.description }}</p>
              </div>
              <p class="d-flex justify-content-between align-items-end">
                <span class="text-decoration-line-through h5">
                  原價: {{ tempProductDetail.origin_price }} 元
                </span>
                <span class="text-danger h3">售價: {{ tempProductDetail.price }} 元</span>
              </p>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <p class="me-3">庫存: {{ tempProductDetail.num }} {{ tempProductDetail.unit }}</p>
          <button type="button" class="btn btn-secondary btn-count m-1"
            :disabled="quantity === 1" @click="quantity--">-</button>
          <span class="border py-2 m-0 rounded btn-count-num text-center">{{ quantity }}</span>
          <button type="button" class="btn btn-secondary btn-count m-1"
            :disabled="quantity >= tempProductDetail.num"
            @click="quantity++">
            +
          </button>
          <button type="button" class="btn btn-danger" @click="addCart">加入購物車</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Modal from 'bootstrap/js/src/modal';
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  components: {
    Loading,
  },
  data() {
    return {
      isLoading: false,
      productDetailModal: null,
      tempProductDetail: {},
      quantity: 1,
    };
  },
  methods: {
    getIntroduction(id) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/product/${id}`;
      this.axios
        .get(api)
        .then((res) => {
          if (res.data.success) {
            this.tempProductDetail = res.data.product;
            this.showModal();
          } else {
            this.$emitter.emit('addMsg', '開啟失敗!');
          }
        })
        .catch((err) => {
          console.dir(err);
        });
    },
    showModal() {
      this.quantity = 1;
      this.productDetailModal.show();
    },
    closeModal() {
      this.productDetailModal.hide();
    },
    addCart() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`;
      const data = {
        product_id: this.tempProductDetail.id,
        qty: this.quantity,
      };
      this.axios.post(api, { data })
        .then((res) => {
          this.isLoading = false;
          if (res.data.success) {
            this.$emitter.emit('addMsg', '成功加入購物車!');
            this.closeModal();
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
  created() {
    this.$emitter.on('getIntroduction', (id) => {
      this.getIntroduction(id);
    });
  },
  mounted() {
    this.productDetailModal = new Modal(this.$refs.productDetailModal, {
      keyboard: false,
    });
  },
};
</script>

<style lang="scss" scoped>
.bg-style {
  height: 500px;
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
}
.btn-count {
  width: 35px;
}
.btn-count-num {
  width: 65px;
}
</style>
