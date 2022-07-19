<template>
  <div class="container">
    <div id="board">
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/one.svg' 
        />
        <input class="ones" />
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/two.svg' 
        />
        <input class="twos" />
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/three.svg' 
        />
        <input class="threes" />
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/four.svg' 
        />
        <input class="fours" />
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/five.svg' 
        />
        <input class="fives" />
      </span>
      <span class="section">
        <img 
          draggable="false"
          src='../assets/dice/six.svg' 
        />
        <input class="sixes" />
      </span>
    </div>
    <div class="dice-wrapper">
      <Dice @selected="selectedDice" :dice="diceFace" :disabled="disabled" />
    </div>
    <p id="total"></p>
    <button :class="{disabled: disabled}" @click="roll">Roll the dice</button>
    <button :class="{disabled: !disabled}" @click="roll">Play</button>
  </div>
</template>

<script setup>
  import { ref, reactive, computed, watch } from 'vue'
  import Dice from './Dice.vue'

  let diceFace = ref([])
  let selected = ref([])
  let rolls = ref(3)
  let disabled = ref(false)

  // when we are out of rolls, send 'disabled' to the dice component
  watch(rolls, (newValue) => {
    if(newValue === 0) {
      disabled.value = true
    }
  })

  const rollDice = () => {
    [...Array(5)].map((el, index) => {
      if(!selected.value.includes(index)) {
        diceFace.value[index] = Math.ceil(Math.random()*6)
      }
    })

    console.log('dice face: ', diceFace.value)
  }

  const roll = () => {
    if(rolls.value === 0) return
    let dice = document.querySelectorAll(".dice-wrapper img")
    dice.forEach((die, index) => {
      if(!selected.value.includes(index)) {
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
    selected.value = [...sel]
    console.log(selected.value)
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
    height: 50px;
    width: 50px;
  }
  .dice-wrapper {
    width: 63%;
    /* display: flex; */
    /* justify-content: space-around; */
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
