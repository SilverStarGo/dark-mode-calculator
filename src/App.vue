<script>
export default {
  data() {
    return {
      output: 0,
      prev: null,
      cur: null,
      operator: null,
      operatorActions: {
        '+': (a, b) => a + b,
        '-': (a, b) => a - b,
        '*': (a, b) => a * b,
        '/': (a, b) => a / b,
      },

      isDarkMode: false,
    };
  },
  methods: {

    toggleMode() {

      console.log("Button clicked!"); // 클릭 이벤트 확인

      this.isDarkMode = !this.isDarkMode;
      // 다크 모드가 활성화되면 body에 클래스를 추가합니다.

      console.log("isDarkMode 상태:", this.isDarkMode);

      if (this.isDarkMode) {
        document.body.classList.add("dark-mode");
      } else {
        document.body.classList.remove("dark-mode");

      }
    },


    clear() { //초기화 로직을 초기화 함수로 분리
      this.output = '0';
      this.prev = null;
      this.cur = null;
      this.operator = null;
    },
    calculate(n) { //연산 흐름을 제어하는 함수로 분리
      // 저장된 숫자가 없는데 연산 기호를 클릭한 경우
      if (!this.cur && !this.prev) {
        alert('숫자를 먼저 입력하세요! ');
        return;
      }
      // 사칙연산 기호를 연달아 클릭한 경우
      if (this.operator !== '' && !this.cur) { // 사칙연산 기호가 공백이 아니면서(이미 누른상태에서), 현재값을 누르지 않고 사칙연산을 클릭했을 경우
        alert('사칙연산 기호를 연달아 누를 수 없습니다! ');
        return;
      }
      // 등호를 클릭해 결과를 노출한 후 다시 등호를 클릭한 경우
      if (n === '=' && this.prev === this.cur) { // 입력값이 등호(=)를 클릭했거나 이전값과 현재값이 같은 형으로 동일할 경우
        return;
      }
      this.cur = Number(this.cur); // this.cur(str->num) 형변환
      if (this.operator !== null) { // 사칙연산 기호면 연산 수행
        // 앞에서 operatorActions를 객체로 정의
        // 객체의 각 속성은 함수를 값으로 가지는 메서드라서 다음처럼 작성 가능
        this.prev = this.operatorActions[this.operator](this.prev, this.cur);
        // 등호면(=) 연산 결과 노출
        if (n === '=') {
          this.output = this.prev;
          this.operator = null;
          this.cur = this.prev;
        } else {
          this.output = null;
          this.operator = n;
          this.cur = null;
        }
      } else { // 등호로 결과를 확인한 후 다시 등호를 클릭했을 때, 즉 등호를 연달아 클릭했을 때 (코드를 멈추고 다음 입력으로 넘어감)
        this.output = null;
        this.operator = n;
        this.prev = this.cur;
        this.cur = null;
      }
    },
    userInput(n) { // 사용자가 입력한 숫자를 저장하는 로직을 함수로 분리
    // 사용자가 입력한 숫자(실제로는 문자열) 저장
    this.cur = this.cur === null ? n : (this.cur +=n);
    // 입력한 값이 출력칸에 표시되도록 output 데이터에 저장
    this.output = this.cur;
    },
    operation(e) {
      // console.log('click'); 콘솔 확인 후 삭제
      // 클릭한 버튼 값 가져오기
      const n = e.currentTarget.value;
      if(n === 'C') {
        this.clear(); // 초기화 함수 호출
      } else if (['+', '-', '*', '/', '='].includes(n)) {
        this.calculate(n); // 연산 흐름 제어 함수 호출
      } else {
        this.userInput(n); // 사용자가 입력한 숫자 저장 함수 호출
      }
    },
  }
}

</script>
<template>

  <div class="app-container" :class="{ dark: isDarkMode }">
    <button class="mode-toggle" @click="toggleMode">
      <div class="toggle" :class="{ 'dark-mode-active': isDarkMode }"></div>
      
        <p>{{ isDarkMode ? "Dark Mode" : "Light Mode" }}</p>
      
    </button>
  </div>

  <div class="calculator">
    <form name="forms">
      <input v-model="output" type="text" name="output" readonly />
      <input type="button" class="clear" value="C" @click="operation" />
      <input type="button" class="operator" value="/" @click="operation" />
      <input type="button" value="1" @click="operation" />
      <input type="button" value="2" @click="operation" />
      <input type="button" value="3" @click="operation" />
      <input type="button" class="operator" value="*" @click="operation" />
      <input type="button" value="4" @click="operation" />
      <input type="button" value="5" @click="operation" />
      <input type="button" value="6" @click="operation" />
      <input type="button" class="operator" value="+" @click="operation" />
      <input type="button" value="7" @click="operation" />
      <input type="button" value="8" @click="operation" />
      <input type="button" value="9" @click="operation" />
      <input type="button" class="operator" value="-" @click="operation" />
      <input type="button" class="dot" value="." @click="operation" />
      <input type="button" value="0" @click="operation" />
      <input type="button" class="operator result" value="=" @click="operation" />
    </form>
  </div>
</template>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background-color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.calculator {
  width: 287px;
  border: 1px solid #333;
  background-color: #ccc;
  padding: 5px;
  border-radius: 20px;
}

.calculator form {
  display: grid;
  grid-template-columns: repeat(4, 65px);
  grid-auto-rows: 65px;
  grid-gap: 5px;
}

.calculator form input {
  border: 2px solid #333;
  cursor: pointer;
  font-size: 19px;
  border-radius: 20px;
}

.calculator form input:hover {
  box-shadow: 1px 1px 2px #333;
}

.calculator form .clear {
  background-color: #ed4848;
}

.calculator form .operator {
  background-color: orange;
}

.calculator form .dot {
  background-color: green;
}

.calculator form input[type='text'] {
  grid-column: span 4;
  text-align: right;
  padding: 0 10px;
}

.calculator form .clear {
  grid-column: span 3;
}

.calculator form .result {
  grid-column: span 2;
}



.app-container {
  display: flex;
  flex-direction: column;
  align-items: end;
  justify-content: center;
  background-color: #fff;
  color: #000;
}

.mode-toggle {
  position: relative;
  padding: 0;
  margin-bottom: 10px;
  width: 144px;
  height: 34px;
  background-color: #ccc;
  border: 0;
  border-radius: 24px;
  cursor: pointer;
  transition: transform 0.5s ease;

}

.mode-toggle .toggle {
  position: absolute;
  top: 2px;
  left: 2px;
  width: 30px;
  height: 30px;
  background-color: white;
  border-radius: 50%;
  transition: transform 0.5s ease;
}

.mode-toggle .toggle.dark-mode-active {
  transform: translateX(110px);
  background-color: #333;
  width: 30px;
  height: 30px;
}

.dark {
  background-color: #333;
  color: #fff;
}

body.dark-mode {
  background-color: #333;
  color: #fff;
}

body.dark-mode .calculator form input {
  background-color: #333;
  color: #fff;
}

body.dark-mode .calculator form .clear {
  background-color: #b8b8b8;
  color: #333;
}

body.dark-mode .calculator form .result {
  background-color: #e2e2e2;
  color: #333;
}


</style>