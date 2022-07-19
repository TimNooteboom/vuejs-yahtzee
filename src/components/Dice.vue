<template>
  <span v-for="(die, index) in props.dice" :key="`die-${index}`" :id="`die-${index+1}`">
    <img 
      @click="selectDice(index)" 
      :class="{ select: selected.includes(index) }"  
      :src=`./src/assets/dice/${dice[die-1]}`  
    /> 
  </span>
</template>

<script setup>
  import { ref } from 'vue'
  const emit = defineEmits(['selected'])

  const props = defineProps({
    dice: {
      type: Array,
      required: true
    }
  })

  const dice = ref(['one.svg', 'two.svg', 'three.svg', 'four.svg', 'five.svg', 'six.svg'])
  const selected = ref([])
  const selectDice = (index) => {
    if(selected.value.includes(index)) {
      selected.value.splice(selected.value.indexOf(index), 1)
    } else {
      selected.value.push(index)
    }
    emit('selected', selected.value)
  }
</script>

<style>
</style>

