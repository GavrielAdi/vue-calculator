<template>
  <div class="calc">
    <div class="display">
      {{ equation }}
    </div>

    <div class="keypad">
      
      <div class="key num" v-for="(num, key) of [1,2,3]" :key="key" @click="useNumber(num)">{{num}}</div>
      <div class="key fn" @click="plus()">+</div>

      <div class="key num" v-for="(num, key) of [4,5,6]" :key="key" @click="useNumber(num)">{{num}}</div>
      <div class="key fn" @click="minus()">-</div>
      
      <div class="key num" v-for="(num, key) of [7,8,9]" :key="key" @click="useNumber(num)">{{num}}</div>
      <div class="key fn" @click="multiply()">x</div>

      <div class="key special" @click="clear()">AC</div>
      <div class="key num" @click="useNumber(0)">0</div>
      <div class="key fn" @click="divide()">/</div>
      <div class="key fn" @click="result()">=</div>

    </div>
  </div>
</template>

<script setup>
  import { ref } from 'vue';

  let equation = ref('0');
  let resultCalled = ref(false);
  let lastResult = ref(0);
  const useNumber = (num) => {
    equation.value = resultCalled.value ? `${num}` : equation.value.search(/^0/g) ? `${equation.value}` + `${num}` : (equation.value.search(/^[-]$/g) !== -1 ? `${equation.value}` + `${num}` : `${num}`);
    resultCalled.value = false;
  };
  const plusOperator = ' + ';
  const plus = () => {
    equation.value = `${checkOperator(equation.value, plusOperator)}`;
  }
  const minusOperator = ' - ';
  const minus = () => {
    equation.value = `${checkOperator(equation.value, minusOperator)}`;
  }
  const multiplyOperator = ' x ';
  const multiply = () => {
    equation.value = `${checkOperator(equation.value, multiplyOperator)}`;
  }
  const divideOperator = ' / ';
  const divide = () => {
    equation.value = `${checkOperator(equation.value, divideOperator)}`;
  }
  const clear = () => { equation.value = '0'}
  const checkOperator = (equation, requestedOperator) => {
    if(equation.search(/^0$/g) !== -1){
      if(requestedOperator.search(/( [/x] )$/g) !== -1) return '0';
      else return requestedOperator.replace(/ /g, '')
    }else{
      if(resultCalled.value){
        resultCalled.value = false;
        return lastResult.value + requestedOperator;
      }else{
        return `${equation.replace(/( [+\-/x] )$/g, '')}${requestedOperator}`;
      }
    }
  }
  const result = () => {
    let finalEqn = equation.value.replace(/( [+\-/x] )$/g, '')
    resultCalled.value = finalEqn.search(/( [+\-/x] )/g) !== -1
    let eqResult = Function('"use strict";return (' + finalEqn.replace(/( \x+ )/g, ' * ') + ')')();
    equation.value = `${eqResult.toLocaleString()}`;
    lastResult.value = eqResult;
  }
</script>

<style scoped>
.calc {
  width: 320px;
  height: 480px;
  display: flex;
  flex-direction: column;
  margin-left: auto;
  margin-right: auto;
  background-color: #1e1e1e;
  border: 2px solid #1e1e1e;
}

.display {
  flex: 1;
  background-color: #d4c7c7;
  margin: 10px;
  font-size: 40px;
  text-align: right;
  overflow-wrap: break-word;
  padding: 5px;
  font-family: "digital";
}

.keypad {
  height: 320px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
  margin: 10px;
}

.key {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 40px;
  cursor: pointer;
}
.num {
  background-color: #525759;
  color: #ffffff;
}
.fn {
  background-color: #877569;
  color: #000000;
}
.special {
  background-color: #BD5A04;
  color: #000000;
  font-size: 35px;
  font-weight: bold;
}
::selection {
  background: none;
}
</style>