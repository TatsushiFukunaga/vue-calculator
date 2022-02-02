<template>
  <div class="history">
    <div v-for="item in items" :key="item">
      <div>
        {{ item }}
        <button class="itemBtn" @click="deleteItem(item)">✖️</button>
      </div>
    </div>
    <button class="btn" v-if="items.length > 0" @click="deleteHistory">
      Delete All
    </button>
  </div>
</template>

<script>
export default {
  props: ["items"],
  methods: {
    /**
     * 計算結果を全て削除
     */
    deleteHistory() {
      this.$emit("deleteHistory", null);
    },
    /**
     * 選択した計算結果を削除
     * @param {int} id - 計算結果のID
     */
    deleteItem(id) {
      this.$emit("deleteItem", id);
    },
  },
};
</script>

<style scoped>
.history {
  padding-top: 60px;
  font-size: 30px;
}

.itemBtn {
  background: #fff;
  border: none;
  color: #fff;
  cursor: pointer;
  opacity: 20%;
}

.itemBtn:hover {
  opacity: 100%;
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
