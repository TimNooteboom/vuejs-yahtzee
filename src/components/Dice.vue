<template>
  <span v-for="(die, index) in props.dice" :key="`die-${index}`" :id="`die-${index+1}`">
    <img 
      @click="selectDice(index)" 
      draggable="false"
      :class="{ select: selected.includes(index) }"  
      :src=`./src/assets/dice/${dice[die-1]}`  
    /> 
  </span>
</template>

<script setup>
  import { ref, watch } from 'vue'
  const emit = defineEmits(['selected'])

  const props = defineProps({
    dice: {
      type: Array,
      required: true
    },
    disabled: {
      type: Boolean,
      default: false
    },
    reset: {
      type: Boolean,
      default: false
    }
  })

  watch(() => props.reset, (newValue) => {
    if(newValue === true) {
      selected.value = []
    }
  })

  const dice = ref(['one.svg', 'two.svg', 'three.svg', 'four.svg', 'five.svg', 'six.svg'])
  const selected = ref([])

  const selectDice = (index) => {
    if(props.disabled) return

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

