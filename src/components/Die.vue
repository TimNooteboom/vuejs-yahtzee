<template>
    <img v-for="(die, index) in 5" :key="`die-${index}`" :id="`die-${index+1}`"  />
</template>

<script setup>

</script>

<style>
</style>