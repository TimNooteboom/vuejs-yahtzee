<template>
  <span v-for="(die, index) in props.dice" :key="`die-${index}`" :id="`die-${index+1}`">
    <Die :die=die-1 />
  </span>
</template>

<script setup>
  import { ref } from 'vue'
  import Die from './Die.vue'
  

  const props = defineProps({
    dice: {
      type: Array,
      required: true
    }
  })

  const selectDice = (index) => {
    console.log(index)
  }
</script>

<style>
</style>

