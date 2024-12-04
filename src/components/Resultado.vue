<template>
  <div>
    <h2>Grupos</h2>
    <div v-for="(grupo, nome) in grupos" :key="nome">
      <h3>{{ nome }}</h3>
      <ul>
        <li v-for="(time, index) in grupo" :key="index">{{ time ? time.name : 'Vago' }}</li>
      </ul>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  grupos: Object
});
</script>

<style scoped>
ul {
  list-style-type: none;
  padding-left: 0;
}
</style>
