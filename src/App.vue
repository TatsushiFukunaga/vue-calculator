<template>
  <div id="app">
    <Calculator @emit-current="setItems" />
    <History
      :items="items"
      @deleteHistory="deleteItems"
      @deleteItem="deleteItem"
    />
  </div>
</template>

<script>
import Calculator from "./components/Calculator.vue";
import History from "./components/History.vue";

export default {
  name: "App",
  components: {
    Calculator,
    History,
  },
  data() {
    return {
      items: [],
    };
  },
  methods: {
    /**
     * 計算結果を配列に追加
     * @param {string} item - 計算結果
     */
    setItems(item) {
      this.items.push(item);
    },
    /**
     * 計算結果を全て削除
     */
    deleteItems() {
      this.items = [];
    },
    /**
     * 選択した計算結果を削除
     * @param {int} key - 計算結果のkey
     */
    deleteItem(key) {
      this.items = this.items.filter((item, index) => key !== index);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
