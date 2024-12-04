<template>
  <div>
    <button @click="iniciarSorteio">Iniciar Sorteio</button>
    <div v-if="timeAtual">
      <h3>Time sorteado: {{ timeAtual.name }}</h3>
      <div>
        <label>Selecione o grupo:</label>
        <select v-model="grupoSelecionado">
          <option v-for="group in gruposDisponiveis" :key="group" :value="group">{{ group }}</option>
        </select>
        <button @click="alocarTime">Alocar Time</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const props = defineProps({
  grupos: Object,
  pots: Array
});

const timeAtual = ref(null);
const grupoSelecionado = ref('');
const gruposDisponiveis = ref(['A', 'B', 'C', 'D', 'E', 'F', 'G', 'H']);

const iniciarSorteio = () => {
  // Sorteio aleatório do time do primeiro pote
  timeAtual.value = props.pots[0].pop(); // Para simplicidade, vamos pegar o primeiro time do Pote 1
  grupoSelecionado.value = gruposDisponiveis.value[0];
};

const alocarTime = () => {
  if (timeAtual.value && grupoSelecionado.value) {
    const grupo = props.grupos[grupoSelecionado.value];
    grupo.push(timeAtual.value);
    timeAtual.value = null;
    grupoSelecionado.value = '';
    emit('atualizar-grupos', props.grupos);
  }
};
</script>

<style scoped>
button {
  margin-top: 10px;
}
</style>
