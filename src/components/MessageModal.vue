<template>
  <section class="position-fixed alert-message">
    <ul>
      <li v-for="(msg, key) in msgs" :key="msg+''+key">
        <div class="alert alert-success" role="alert">
          {{ msg }}
        </div>
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  data() {
    return {
      msgs: [],
    };
  },
  methods: {
    addMsg(msg) {
      this.msgs.push(msg);
      setTimeout(() => {
        this.msgs.shift();
      }, 5000);
    },
  },
  created() {
    this.$emitter.on('addMsg', (msg) => {
      this.addMsg(msg);
    });
  },
};
</script>

<style scoped>
ul {
  list-style: none;
}
.alert-message {
  top: 30px;
  right: 0;
}
</style>
