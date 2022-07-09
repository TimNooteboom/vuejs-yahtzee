<template>
  <div class="container">
    <div class="dice-wrapper">
      <Dice :dice = "diceFace" />
    </div>
    <p id="total"></p>
    <button @click="roll">Roll the dice</button>
  </div>
</template>

<script setup>
  import { ref, reactive } from 'vue'
  import Dice from './Dice.vue'

  let diceFace = ref([])

  const rollDice = () => {

    diceFace.value = [...Array(5)].map(() => {
      return Math.ceil(Math.random()*6)
    })

    console.log(diceFace.value)

    // document.querySelector('#total').innerHTML = 'The total is ' + ((dieOneValue+1) + (dieTwoValue+1) + (dieThreeValue+1) + (dieFourValue+1) + (dieFiveValue+1)) 
  }
  const roll = () => {

    let dice = document.querySelectorAll(".dice-wrapper img")
    dice.forEach((die) => {
      die.classList.add('shake')
    })

    setTimeout(() => {
      dice.forEach((die) => {
        die.classList.remove('shake')
      })
      rollDice()
    }, 1000)

  }
</script>

<style>
  * {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-family: "Poppins", sans-serif;
    padding: 20px;
    background-color: #fff;
    border: 1px solid;
    box-shadow: 0 5px 35px rgb(50,50,50,0.15);
    border-radius: 8px;
  }
  .dice-wrapper {
    width: 63%;
    /* display: flex; */
    /* justify-content: space-around; */
  }
  .dice-wrapper img {
    height: 100px;
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
