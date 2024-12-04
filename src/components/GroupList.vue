<template>
  <div>
    <h2>Grupos:</h2>
    <ul>
      <li v-for="(group, groupName) in groups" :key="groupName">
        <strong>{{ groupName }}:</strong>
        <ul>
          <li v-for="(team, index) in group" :key="index">
            {{ team ? team.name : 'Vazio' }}
          </li>
        </ul>
      </li>
    </ul>
  </div>
</template>

<script setup>
defineProps({
  groups: Object
});
</script>
