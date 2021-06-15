<template>
  <Loading v-model:active="isLoading" />
  <section class="w-50 mx-auto py-5">
    <h2>填寫訂單</h2>
    <Form v-slot="{ errors }" @submit="onSubmit">
      <div class="mb-3">
        <label for="name" class="form-label">姓名</label>
        <Field id="name" name="姓名" type="text"
          class="form-control" :class="{ 'is-invalid': errors['姓名'] }"
          placeholder="請輸入 姓名" rules="required" v-model="user.name">
        </Field>
        <error-message name="姓名" class="invalid-feedback"></error-message>
        <label for="email" class="form-label">Email</label>
        <Field id="email" name="email" type="email"
          class="form-control" :class="{ 'is-invalid': errors['email'] }"
          placeholder="請輸入 Email" rules="email|required" v-model="user.email">
        </Field>
        <error-message name="email" class="invalid-feedback"></error-message>
        <label for="tel" class="form-label">電話</label>
        <Field id="tel" name="電話" type="number"
          class="form-control" :class="{ 'is-invalid': errors['電話'] }"
          placeholder="請輸入 電話" :rules="validateTel" v-model="user.tel">
        </Field>
        <error-message name="電話" class="invalid-feedback" as="span">
          <span>請填寫正確號碼!</span>
        </error-message>
        <label for="address" class="form-label">地址</label>
        <Field id="address" name="地址" type="text"
          class="form-control" :class="{ 'is-invalid': errors['地址'] }"
          placeholder="請輸入 地址" rules="required" v-model="user.address">
        </Field>
        <error-message name="地址" class="invalid-feedback"></error-message>
        <label for="message" class="form-label">留言</label>
        <textarea id="message" type="text" rows="10" class="w-100" v-model="message"></textarea>
      </div>
      <button class="btn btn-primary" type="submit">送出訂單</button>
    </Form>
  </section>
</template>

<script>
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';

export default {
  name: 'CheckOut',
  components: {
    Loading,
  },
  data() {
    return {
      isLoading: false,
      user: {},
      message: '',
      cartStatus: false,
    };
  },
  methods: {
    onSubmit(value, { resetForm }) {
      if (!this.cartStatus) {
        this.$emitter.emit('addMsg', '購物車是空的!');
        return;
      }
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/order`;
      const data = {
        user: this.user,
        message: this.message,
      };
      this.axios.post(api, { data })
        .then((res) => {
          console.log(res);
          this.isLoading = false;
          if (res.data.success) {
            this.$emitter.emit('addMsg', '成功送出訂單!');
            resetForm();
            this.message = '';
            this.$emitter.emit('getCarts');
          } else {
            this.$emitter.emit('addMsg', '訂單失敗!');
          }
        })
        .catch((err) => {
          console.dir(err);
        });
    },
    validateTel() {
      const telRules = /^09[0-9]{8}$/;
      return telRules.test(this.user.tel);
    },
  },
  created() {
    this.$emitter.on('checkCartStatus', (status) => {
      this.cartStatus = status;
    });
  },
};
</script>
