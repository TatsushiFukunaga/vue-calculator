<template>
  <div class="calculator">
    <div class="display">{{ current || "0" }}</div>
    <div @click="clear" class="btn top">C</div>
    <div @click="sign" class="btn top">+/-</div>
    <div @click="percent" class="btn top">%</div>
    <div @click="divide" class="btn operator">÷</div>
    <div @click="append('7')" class="btn">7</div>
    <div @click="append('8')" class="btn">8</div>
    <div @click="append('9')" class="btn">9</div>
    <div @click="times" class="btn operator">x</div>
    <div @click="append('4')" class="btn">4</div>
    <div @click="append('5')" class="btn">5</div>
    <div @click="append('6')" class="btn">6</div>
    <div @click="minus" class="btn operator">-</div>
    <div @click="append('1')" class="btn">1</div>
    <div @click="append('2')" class="btn">2</div>
    <div @click="append('3')" class="btn">3</div>
    <div @click="add" class="btn operator">+</div>
    <div @click="append('0')" class="btn zero">0</div>
    <div @click="dot" class="btn">.</div>
    <div @click="equal" class="btn operator">=</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      previous: "",
      current: "",
      operator: null,
      operatorClicked: false,
    };
  },
  methods: {
    clear() {
      this.current = "";
    },
    sign() {
      this.current =
        this.current.charAt(0) === "-"
          ? this.current.slice(1)
          : `-${this.current}`;
    },
    percent() {
      this.current = `${parseFloat(this.current) / 100}`;
    },
    append(number) {
      if (this.operatorClicked) {
        this.current = "";
        this.operatorClicked = false;
      }
      this.current = `${this.current}${number}`;
    },
    dot() {
      if (this.current.indexOf(".") === -1) {
        this.append(".");
      }
    },
    divide() {
      this.operator = (a, b) => b / a;
      this.previous = this.current;
      this.operatorClicked = true;
    },
    times() {
      this.operator = (a, b) => a * b;
      this.previous = this.current;
      this.operatorClicked = true;
    },
    minus() {
      this.operator = (a, b) => b - a;
      this.previous = this.current;
      this.operatorClicked = true;
    },
    add() {
      this.operator = (a, b) => a + b;
      this.previous = this.current;
      this.operatorClicked = true;
    },
    equal() {
      this.current = `${this.operator(
        parseFloat(this.current),
        parseFloat(this.previous)
      )}`;
      this.previous = "";
    },
  },
};
</script>

<style scoped>
.calculator {
  margin: 0 auto;
  width: 400px;
  font-size: 40px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(50px, auto);
}

.display {
  grid-column: 1/5;
  background: black;
  color: white;
  margin-bottom: 8px;
}

.zero {
  grid-column: 1/3;
}

.btn {
  color: white;
  background-color: #3b3b3b;
  border: 1px solid #999;
  border-radius: 30%;
}

.btn:active {
  background-color: rgb(139, 139, 139);
}

.top {
  background-color: rgb(206, 203, 203);
  color: black;
}

.top:active {
  background-color: rgb(231, 231, 231);
}

.operator {
  background-color: orange;
  color: white;
}

.operator:active {
  background-color: rgb(248, 211, 141);
}
</style>
