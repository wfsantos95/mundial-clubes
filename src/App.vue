<template>
  <div id="app">
    <h1>Sorteio de Times para os Grupos</h1>
    <Sorteio :pots="pots" :groups="groups" @sorteio-concluido="onSorteioConcluido" />
    <div class="p-8">
      <div class="grid grid-cols-1 gap-6 md:grid-cols-2 lg:grid-cols-4">
        <!-- Criar uma tabela para cada grupo -->
        <div v-for="(group, groupName) in groups" :key="groupName" class="relative overflow-x-auto shadow-md sm:rounded-lg">
          <table class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
            <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
            <tr>
              <th scope="col" class="px-6 py-3">{{ `Grupo ${groupName}` }}</th>
            </tr>
            </thead>
            <tbody>
            <!-- Preencher os espaços com base na posição do time -->
            <tr v-for="(team, index) in group" :key="index" class="bg-gray-800">
              <td class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white">
                  <span v-if="team" class="relative">
                    <Popover class="relative">
                      <PopoverButton class="text-blue-500 hover:text-blue-700">
                        {{ team.name }}
                      </PopoverButton>
                      <PopoverPanel class="absolute z-10 p-4 mt-2 text-xs text-white bg-black rounded-lg shadow-lg max-w-xs">
                        <ul>
                          <li v-for="(rule, index) in getValidationRules(team)" :key="index" :class="rule.isValid ? 'text-green-500' : 'text-red-500'">
                            {{ rule.message }}
                          </li>
                        </ul>
                      </PopoverPanel>
                    </Popover>
                  </span>
                <span v-else>Vazio</span>
              </td>
            </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import {Popover, PopoverButton, PopoverPanel} from '@headlessui/vue';
import Sorteio from './components/Sorteio.vue';

const pots = ref({
  1: [
    { name: "Manchester City", confederation: "UEFA", country: "ING" },
    { name: "Real Madrid C. F.", confederation: "UEFA", country: "ESP" },
    { name: "FC Bayern München", confederation: "UEFA", country: "ALE" },
    { name: "Paris Saint-Germain", confederation: "UEFA", country: "FRA" },
    { name: "CR Flamengo", confederation: "CONMEBOL", country: "BRA" },
    { name: "SE Palmeiras", confederation: "CONMEBOL", country: "BRA" },
    { name: "CA River Plate", confederation: "CONMEBOL", country: "ARG" },
    { name: "Fluminense FC", confederation: "CONMEBOL", country: "BRA" }
  ],
  2: [
    { name: "Chelsea FC", confederation: "UEFA", country: "ING" },
    { name: "Borussia Dortmund", confederation: "UEFA", country: "ALE" },
    { name: "FC Internazionale Milano", confederation: "UEFA", country: "ITA" },
    { name: "FC Porto", confederation: "UEFA", country: "POR" },
    { name: "Atlético de Madrid", confederation: "UEFA", country: "ESP" },
    { name: "SL Benfica", confederation: "UEFA", country: "POR" },
    { name: "Juventus FC", confederation: "UEFA", country: "ITA" },
    { name: "FC Salzburg", confederation: "UEFA", country: "AUT" }
  ],
  3: [
    { name: "Al Hilal", confederation: "AFC", country: "SAU" },
    { name: "Ulsan HD", confederation: "AFC", country: "COR" },
    { name: "Al Ahly FC", confederation: "CAF", country: "EGI" },
    { name: "Wydad AC", confederation: "CAF", country: "MAR" },
    { name: "CF Monterrey", confederation: "CONCACAF", country: "MEX" },
    { name: "Club León", confederation: "CONCACAF", country: "MEX" },
    { name: "CA Boca Juniors", confederation: "CONMEBOL", country: "ARG" },
    { name: "Botafogo", confederation: "CONMEBOL", country: "BRA" }
  ],
  4: [
    { name: "Urawa Red Diamonds", confederation: "AFC", country: "JAP" },
    { name: "Al Ain FC", confederation: "AFC", country: "EAU" },
    { name: "Espérance Sportive de Tunisie", confederation: "CAF", country: "TUN" },
    { name: "Mamelodi Sundowns FC", confederation: "CAF", country: "RSA" },
    { name: "CF Pachuca", confederation: "CONCACAF", country: "MEX" },
    { name: "Auckland City FC", confederation: "OFC", country: "NZL" },
  ]
});

const groups = ref({
  A: [null, null, null, { name: "Inter Miami CF", confederation: "CONCACAF", country: "EUA" }],
  B: [null, null, null, { name: "Seattle Sounders FC", confederation: "CONCACAF", country: "EUA" }],
  C: [null, null, null, null],
  D: [null, null, null, null],
  E: [null, null, null, null],
  F: [null, null, null, null],
  G: [null, null, null, null],
  H: [null, null, null, null]
});

const isSorteioConcluido = ref(false);

function onSorteioConcluido(updatedGroups) {
  groups.value = updatedGroups;
  isSorteioConcluido.value = true;
}

function getValidationRules(team) {
  // Exemplo de validação de regras para a equipe
  const rules = [];

  if (team.confederation === 'UEFA') {
    rules.push({
      message: 'Este time está alocado corretamente na confederação UEFA.',
      isValid: true
    });
  } else {
    rules.push({
      message: 'Este time não pode ser alocado em uma confederação incorreta.',
      isValid: false
    });
  }

  if (team.country !== 'BRA') {
    rules.push({
      message: 'Este time não é brasileiro, e a alocação está correta.',
      isValid: true
    });
  } else {
    rules.push({
      message: 'Este time é brasileiro e a alocação está incorreta!',
      isValid: false
    });
  }

  return rules;
}
</script>

<style scoped>
/* Estilo do Popover */
table td {
  position: relative;
}
</style>
