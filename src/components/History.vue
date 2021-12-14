<template>
  <div class="history">
    <div v-for="item in items" :key="item">
      <div>{{ item }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["items"],
};
</script>

<style scoped>
.history {
  padding-top: 60px;
  font-size: 30px;
}
</style>
