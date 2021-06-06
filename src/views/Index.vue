<template>
  <main>
    <ProductList />
    <ProductDetailModal />
    <CartList />
    <CheckOut />
    <MessageModal />
  </main>
</template>

<script>
import ProductList from '@/components/ProductList.vue';
import ProductDetailModal from '@/components/ProductDetailModal.vue';
import CartList from '@/components/CartList.vue';
import CheckOut from '@/components/CheckOut.vue';
import MessageModal from '@/components/MessageModal.vue';

export default {
  name: 'App',
  components: {
    ProductList,
    ProductDetailModal,
    CartList,
    CheckOut,
    MessageModal,
  },
};
</script>
