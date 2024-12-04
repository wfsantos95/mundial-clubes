<template>
  <div>
    <h2>Potes Restantes:</h2>
    <ul>
      <li v-for="(pot, potIndex) in pots" :key="potIndex">
        <strong>Pote {{ potIndex }}:</strong>
        <ul>
          <li v-for="(team, index) in pot" :key="index">
            {{ team.name }}
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<script setup>
defineProps({
  pots: Object
});
</script>
