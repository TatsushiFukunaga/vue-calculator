<template>
  <div class="calculator">
    <div class="display">{{ display || "0" }}</div>
    <div @click="clear" class="btn top">C</div>
    <div @click="sign" class="btn top">+/-</div>
    <div @click="percent" class="btn top">%</div>
    <div @click="divide" class="btn operator">÷</div>
    <div @click="append('7')" class="btn">7</div>
    <div @click="append('8')" class="btn">8</div>
    <div @click="append('9')" class="btn">9</div>
    <div @click="times" class="btn operator">×</div>
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
import BigNumber from "bignumber.js";
export default {
  data() {
    return {
      display: "",
      previous: "",
      current: "",
      operator: null,
      operatorForDecimal: null,
      operatorClicked: false,
      equalClicked: false,
    };
  },
  methods: {
    /**
     * 全ての変数を初期値に戻す
     */
    clear() {
      this.current = "";
      this.previous = "";
      this.display = "";
      this.operator = null;
      this.operatorForDecimal = null;
    },
    /**
     * currentとdisplayの符号を反転させる
     */
    sign() {
      if (this.operatorClicked) {
        this.current = "-0";
        this.display = `${this.display}(-0)`;
        this.operatorClicked = false;
      } else if (this.equalClicked) {
        this.current = "-0";
        this.display = "-0";
        this.equalClicked = false;
      } else {
        if (this.current === "") this.current = "0";
        this.current =
          this.current.charAt(0) === "-"
            ? this.current.slice(1)
            : `-${this.current}`;

        if (this.current.includes("-")) {
          if (
            this.display.slice(-this.current.length).includes("+") ||
            this.display.slice(-this.current.length).includes("-") ||
            this.display.slice(-this.current.length).includes("×") ||
            this.display.slice(-this.current.length).includes("÷")
          ) {
            this.display = `${this.display.slice(
              0,
              this.display.length - this.current.length + 1
            )}(${this.current})`;
          } else {
            this.display = `${this.display.slice(
              0,
              this.display.length - this.current.length + 1
            )}${this.current}`;
          }
        } else {
          if (this.display.slice(-this.current.length - 3).includes("(")) {
            this.display = `${this.display.slice(
              0,
              this.display.length - this.current.length - 3
            )}${this.current}`;
          } else {
            this.display = `${this.display.slice(
              0,
              this.display.length - this.current.length - 1
            )}${this.current}`;
          }
        }
      }
    },
    /**
     * currentを1/100
     */
    percent() {
      if (!this.equalClicked && !this.operatorClicked) {
        if (this.current === "") {
          this.current = "0";
          this.display = "0";
        } else {
          const length = this.current.length;
          this.current = this.current.includes(".")
            ? `${BigNumber(parseFloat(this.current)).div(100)}`
            : `${parseFloat(this.current) / 100}`;
          this.display =
            this.display.slice(-1) === ")"
              ? `${this.display.slice(0, -length - 1)}${this.current})`
              : `${this.display.slice(0, -length)}${this.current}`;
        }
      }
    },
    /**
     * displayに数字を追加
     * @param {string} number 入力された数字
     */
    append(number) {
      if (this.equalClicked) {
        this.current = number;
        this.display = number;
        this.equalClicked = false;
      } else if (this.operatorClicked) {
        this.current = number;
        this.display = `${this.display}${number}`;
        this.operatorClicked = false;
      } else if (this.current === "0") {
        this.current = number;
        this.display = `${this.display.slice(0, -1)}${number}`;
      } else if (this.current === "-0") {
        this.current = `-${number}`;
        this.display =
          this.display === "-0"
            ? `${this.display.slice(0, -2)}-${number}`
            : `${this.display.slice(0, -4)}(-${number})`;
      } else {
        this.current = `${this.current}${number}`;
        this.display =
          this.display.slice(-1) === ")"
            ? `${this.display.slice(0, -1)}${number})`
            : `${this.display}${number}`;
      }
    },
    /**
     * displayに.を追加
     */
    dot() {
      if (this.equalClicked || this.current === "") {
        this.append("0");
        this.equalClicked = false;
      } else if (this.operatorClicked) {
        this.append("0.");
        this.operatorClicked = false;
      }
      if (this.current === "0" || this.current === "-0") this.append("0.");
      if (!this.current.includes(".")) this.append(".");
    },
    /**
     * 計算を実行
     * @param {function} callback - 計算実行後に実行する関数
     */
    execute(callback) {
      if (
        this.previous !== "" &&
        this.current !== "" &&
        !this.operatorClicked
      ) {
        this.current =
          this.previous.includes(".") || this.current.includes(".")
            ? `${this.operatorForDecimal(
                parseFloat(this.current),
                parseFloat(this.previous)
              )}`
            : `${this.operator(
                parseFloat(this.current),
                parseFloat(this.previous)
              )}`;
        if (typeof callback === "function" && callback()) {
          callback();
        }
      }
    },
    /**
     * 計算式をemitする
     */
    emitCurrent() {
      this.$emit("emit-current", `${this.display}=${this.current}`);
      this.previous = "";
      this.operator = null;
      this.operatorForDecimal = null;
      this.equalClicked = true;
    },
    setPrevious() {
      this.previous = this.current;
      this.operatorClicked = true;
    },
    /**
     * displayにoperatorを追加する
     * @param {string} operator - 入力されたoperator
     */
    addOperatorOnDisplay(operator) {
      if (
        this.display.slice(-1) == "+" ||
        this.display.slice(-1) == "-" ||
        this.display.slice(-1) == "×" ||
        this.display.slice(-1) == "÷"
      ) {
        if (operator !== "=") {
          this.display = `${this.display.slice(0, -1)}${operator}`;
        }
      } else if (!this.display.includes("=") && this.current !== "") {
        this.display =
          operator === "="
            ? `${this.display}${operator}${this.current}`
            : `${this.display}${operator}`;
      }
    },
    divide() {
      if (this.equalClicked) {
        this.display = this.current;
        this.equalClicked = false;
      }
      if (this.current !== "") {
        this.execute();
        this.operator = (a, b) => b / a;
        this.operatorForDecimal = (a, b) => BigNumber(b).div(a);
        this.setPrevious();
        this.addOperatorOnDisplay("÷");
      }
    },
    times() {
      if (this.equalClicked) {
        this.display = this.current;
        this.equalClicked = false;
      }
      if (this.current !== "") {
        this.execute();
        this.operator = (a, b) => a * b;
        this.operatorForDecimal = (a, b) => BigNumber(a).times(b);
        this.setPrevious();
        this.addOperatorOnDisplay("×");
      }
    },
    minus() {
      if (this.equalClicked) {
        this.display = this.current;
        this.equalClicked = false;
      }
      if (this.current !== "") {
        this.execute();
        this.operator = (a, b) => b - a;
        this.operatorForDecimal = (a, b) => BigNumber(b).minus(a);
        this.setPrevious();
        this.addOperatorOnDisplay("-");
      }
    },
    add() {
      if (this.equalClicked) {
        this.display = this.current;
        this.equalClicked = false;
      }
      if (this.current !== "") {
        this.execute();
        this.operator = (a, b) => a + b;
        this.operatorForDecimal = (a, b) => BigNumber(a).plus(b);
        this.setPrevious();
        this.addOperatorOnDisplay("+");
      }
    },
    equal() {
      if (this.operator !== null) {
        this.execute(this.emitCurrent);
        this.addOperatorOnDisplay("=");
      }
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
  user-select: none;
}

.display {
  grid-column: 1/5;
  background: black;
  color: white;
  margin-bottom: 8px;
  text-align: right;
  padding: 8px;
}

.zero {
  grid-column: 1/3;
}

.btn {
  color: white;
  background-color: #3b3b3b;
  border: 1px solid #999;
  cursor: pointer;
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
