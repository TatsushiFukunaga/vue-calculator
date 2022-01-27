<template>
  <div class="history">
    <div v-for="item in items" :key="item">
      <div>{{ item }}</div>
    </div>
    <button class="btn" v-if="items.length > 0" @click="deleteHistory">
      Delete
    </button>
  </div>
</template>

<script>
export default {
  props: ["items"],
  methods: {
    deleteHistory() {
      this.$emit("deleteHistory", null);
    },
  },
};
</script>

<style scoped>
.history {
  padding-top: 60px;
  font-size: 30px;
}

.btn {
  border: 2px solid #000;
  background: #fff;
  font-size: 15px;
  cursor: pointer;
}

.btn:hover {
  color: #fff;
  background: #000;
}
</style>
