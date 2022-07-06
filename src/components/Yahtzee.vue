<template>
  <div class="container">
    <div class="dice-wrapper">
      <Die />
    </div>
    <p id="total"></p>
    <button @click="roll">Roll the dice</button>
  </div>
</template>

<script setup>
  import { ref, reactive } from 'vue'
  import Die from './Die.vue'

  const diceFace = reactive([
    { name: 'dieOneValue', img: 'one.svg'}, 
    { name: 'dieTwoValue', img: 'two.svg' }, 
    { name: 'dieThreeValue', img: 'three.svg' }, 
    { name: 'dieFourValue', img: 'four.svg' }, 
    { name: 'dieFiveValue', img: 'five.svg' }, 
    { name: 'dieSixValue', img: 'six.svg' }
  ])

  const rollDice = () => {
    // let dieOneValue = Math.floor(Math.random()*6)
    // let dieTwoValue = Math.floor(Math.random()*6)
    // let dieThreeValue = Math.floor(Math.random()*6)
    // let dieFourValue = Math.floor(Math.random()*6)
    // let dieFiveValue = Math.floor(Math.random()*6)

    // document.querySelector('#die-1').setAttribute('src', '/src/assets/dice/' + diceFace[dieOneValue])
    // document.querySelector('#die-2').setAttribute('src', '/src/assets/dice/' + diceFace[dieTwoValue])
    // document.querySelector('#die-3').setAttribute('src', '/src/assets/dice/' + diceFace[dieThreeValue])
    // document.querySelector('#die-4').setAttribute('src', '/src/assets/dice/' + diceFace[dieFourValue])
    // document.querySelector('#die-5').setAttribute('src', '/src/assets/dice/' + diceFace[dieFiveValue])

    diceFace.forEach((die) => {
      die.val = Math.ceil(Math.random()*6)
    })

    console.log(diceFace)

    // document.querySelector('#total').innerHTML = 'The total is ' + ((dieOneValue+1) + (dieTwoValue+1) + (dieThreeValue+1) + (dieFourValue+1) + (dieFiveValue+1)) 
  }
  const roll = () => {
    const word = ''
    const image = ''
    const value = 0
    // diceFace.forEach((img, i) => {
    //   console.log(i+1, img.img.split('.')[0], img)
    // })

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
  // roll()
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
    width: 60%;
    /* display: flex; */
    /* justify-content: space-around; */
  }
  .dice-wrapper img {
    height: 100px;
    color: #e92e3d;
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
