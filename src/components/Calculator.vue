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
      equalClicked: false,
    };
  },
  methods: {
    clear() {
      this.current = "";
      this.previous = "";
    },
    sign() {
      if (this.current === "") {
        this.current = "0";
      }
      this.current =
        this.current.charAt(0) === "-"
          ? this.current.slice(1)
          : `-${this.current}`;
    },
    percent() {
      if (this.current === "") {
        this.current = "0";
      } else {
        this.current = `${parseFloat(this.current) / 100}`;
      }
    },
    append(number) {
      if (this.operatorClicked || this.equalClicked || this.current === "0") {
        this.current = number;
        this.operatorClicked = false;
        this.equalClicked = false;
      } else if (this.current === "-0") {
        this.current = `-${number}`;
      } else {
        this.current = `${this.current}${number}`;
      }
    },
    dot() {
      if (
        this.operatorClicked ||
        this.equalClicked ||
        this.current === "" ||
        this.current === "0"
      ) {
        this.current = "0.";
        this.operatorClicked = false;
        this.equalClicked = false;
      }
      if (!this.current.includes(".")) {
        this.append(".");
      }
    },
    execute(callback) {
      if (
        this.previous !== "" &&
        this.current !== "" &&
        !this.operatorClicked
      ) {
        this.current = `${this.operator(
          parseFloat(this.current),
          parseFloat(this.previous)
        )}`;
        if (typeof callback === "function" && callback()) {
          callback();
        }
      }
    },
    emitCurrent() {
      this.$emit("emit-current", this.current);
      this.previous = "";
      this.equalClicked = true;
    },
    setPrevious() {
      this.previous = this.current;
      this.operatorClicked = true;
    },
    divide() {
      this.execute();
      this.operator = (a, b) => b / a;
      this.setPrevious();
    },
    times() {
      this.execute();
      this.operator = (a, b) => a * b;
      this.setPrevious();
    },
    minus() {
      this.execute();
      this.operator = (a, b) => b - a;
      this.setPrevious();
    },
    add() {
      this.execute();
      this.operator = (a, b) => a + b;
      this.setPrevious();
    },
    equal() {
      this.execute(this.emitCurrent);
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
  text-align: right;
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
