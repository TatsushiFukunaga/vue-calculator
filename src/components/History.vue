<template>
  <div class="history">
    <div v-for="item in items" :key="item">
      <div>{{ item }}</div>
    </div>
    <button v-if="items.length > 0" @click="deleteHistory">Delete</button>
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
</style>
