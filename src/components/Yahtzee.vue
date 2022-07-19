<template>
  <div class="container">
    <div id="board">
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/one.svg' 
        />
        <input @click="input(1)" class="ones" v-model="values[0]" />
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/two.svg' 
        />
        <input @click=input(2) class="twos"  v-model="values[1]"/>
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/three.svg' 
        />
        <input @click=input(3) class="threes"  v-model="values[2]"/>
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/four.svg' 
        />
        <input @click=input(4) class="fours"  v-model="values[3]"/>
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/five.svg' 
        />
        <input @click=input(5) class="fives"  v-model="values[4]"/>
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/six.svg' 
        />
        <input @click=input(6) class="sixes"  v-model="values[5]"/>
      </span>
    </div>
    <div class="dice-wrapper">
      <Dice @selected="selectedDice" :dice="diceFace" :disabled="disabled" :reset="reset" />
    </div>
    <p id="total"></p>
    <button :class="{disabled: disabled}" @click="roll">Roll the dice</button>
    <button :class="{disabled: rolls == 3 || !viewed}" @click="submit">Play</button>
  </div>
</template>

<script setup>
  import { ref, reactive, computed, watch } from 'vue'
  import Dice from './Dice.vue'

  let diceFace = ref([])
  let selectedCopy = ref([])
  let rolls = ref(3)
  let disabled = ref(false)
  let viewed = ref(false)
  let reset = ref(false)
  let values = ref(['','','','','',''])
  let currentValue = {}

  // when we are out of rolls, send 'disabled' to the dice component
  watch(rolls, (newValue) => {
    if(newValue === 0) {
      disabled.value = true
    }
  })

  const input = (index) => {
    viewed.value = true
    // filter out the diceFace array to only show the values that was clicked on, then add them up
    const valueArr = diceFace.value.filter((el) => el === index)
    let value = 0

    if(valueArr.length) {
      value = valueArr.reduce((a,b) => a + b)
    }

    currentValue = {
      value: value,
      index: index-1
    }
    
    console.log(currentValue)
  }

  const submit = () => {
    values.value[currentValue.index] = currentValue.value
    if(!values.value.some((el) => el === '')) {
      console.log('game over!!') // game is over. Reset Everything. Remove the dice from board
    }

    console.log(values.value)
    rolls.value = 3
    disabled.value = false
    selectedCopy.value = []
    reset.value = true
  }

  const rollDice = () => {
    [...Array(5)].map((el, index) => {
      if(!selectedCopy.value.includes(index)) {
        diceFace.value[index] = Math.ceil(Math.random()*6)
      }
    })

    console.log('dice face: ', diceFace.value)
  }

  const roll = () => {
    reset.value = false
    viewed.value = false
    if(rolls.value === 0) return
    let dice = document.querySelectorAll(".dice-wrapper img")
    dice.forEach((die, index) => {
      if(!selectedCopy.value.includes(index)) {
        die.classList.add('shake')
      }
    })

    setTimeout(() => {
      dice.forEach((die) => {
        die.classList.remove('shake')
      })
      rollDice()
    }, 1000)
    rolls.value--
  }
  const selectedDice = (sel) => {
    selectedCopy.value = [...sel]
    console.log(selectedCopy.value)
  }
</script>

<style>
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }
  .container {
    width: 500px;
    /* display: flex; */
    font-family: "Poppins", sans-serif;
    padding: 20px;
    margin: 20px auto;
    background-color: #fff;
    border: 1px solid;
    box-shadow: 0 5px 35px rgb(50,50,50,0.15);
    border-radius: 8px;
  }
  .disabled {
    filter: opacity(0.5)
  }
  #board {
    width: 100px;
    margin-bottom: 20px;
    flex-direction: column;
  }
  #board .section {
    flex-direction: row;
    width: 200px;
  }
  #board .section img {
    height: 50px;
  }
  #board .section input {
    height: 45px;
    width: 45px;
    padding: 5px;
    font-size: 20px;
  }
  .dice-wrapper {
    width: 265px;
    border: 2px solid;
    height: 65px;
    padding: 5px;
  }
  .dice-wrapper img {
    height: 50px;
  }
  .dice-wrapper img.select {
    filter: opacity(0.5)
  }
  p {
    font-size: 16px;
    margin: 30px 0;
    font-weight: 500;
  }
  button {
    background-color: #e92e3d;
    border: none;
    outline: none;
    color: #fff;
    padding: 15px 0;
    width: 150px;
    margin-right: 20px;
    letter-spacing: 1px;
    border-radius: 5px;
    text-transform: uppercase;
    cursor: pointer;
  }
  .shake {
    animation: shake 0.5s infinite;
  }
  @keyframes shake {
    0% {
      transform: rotate(8deg);
    }
    50% {
      transform: rotate(-8deg);
    }
    100% {
      transform: rotate(8deg);
    }
  }
</style>
