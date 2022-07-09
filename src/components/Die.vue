<template>
   <img 
        @click="selectDice" 
        :class="{ select: isSelected }"  
        :src=`./src/assets/dice/${dice[props.die]}`  
    />                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      
</template>

<script setup>
import { ref } from 'vue'

let isSelected = ref(false)
const dice = ref(['one.svg', 'two.svg', 'three.svg', 'four.svg', 'five.svg', 'six.svg'])

const props = defineProps({
    die: {
        type: Number,
      required: true
    },
  })

const selectDice = () => {
    isSelected = !isSelected
    console.log(isSelected)
} 
</script>

<style>
</style>