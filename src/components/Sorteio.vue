<template>
  <div>
    <button @click="startDraw">Iniciar Sorteio</button>
  </div>
</template>

<script setup>

const props = defineProps({
  pots: Object,
  groups: Object
});

const emit = defineEmits(['sorteio-concluido']);

const startDraw = async () => {
  await simulateDraw();
};

const simulateDraw = async () => {
  // Aloca os times no grupo, conforme a lógica existente
  await allocateTeamsFromRemainingPots();
};

// const allocateTeamsFromRemainingPots = async () => {
//   const updatedGroups = {...props.groups};
//   const updatedPots = {...props.pots};
//
//   // Função para embaralhar os times de cada pote
//   const shuffle = (array) => {
//     return array.sort(() => Math.random() - 0.5);
//   };
//
//   // Função para alocar times em grupos, considerando o algoritmo já validado
//   const assignTeamToGroup = async (team, startGroupIndex = 0) => {
//     const groupKeys = Object.keys(updatedGroups);
//
//     // Percorre os grupos começando do grupo indicado por startGroupIndex
//     for (let i = startGroupIndex; i < groupKeys.length; i++) {
//       console.log(i, groupKeys[i])
//
//       const group = groupKeys[i];
//
//       // Verifica se o time pode ser colocado no grupo (restrições de confederação e país)
//       const uefaTeamsInGroup = updatedGroups[group].filter(g => g && g.confederation === "UEFA").length;
//
//       if (
//           updatedGroups[group].includes(null) && // Verifica se o grupo tem espaço
//           (team.confederation !== "UEFA" || uefaTeamsInGroup < 2) && // Limite de 2 times da UEFA por grupo
//           updatedGroups[group].every(
//               g => !g || g.confederation !== team.confederation && g.country !== team.country
//           )
//       ) {
//         // Atribui o time à primeira posição livre no grupo
//         const emptyIndex = updatedGroups[group].indexOf(null);
//         updatedGroups[group][emptyIndex] = team;
//
//         // Remove o time do pote após a alocação
//         const potIndex = Object.keys(updatedPots).find(pot => updatedPots[pot].includes(team));
//         if (potIndex) {
//           updatedPots[potIndex] = updatedPots[potIndex].filter(t => t !== team);
//         }
//
//
//         logState(updatedGroups, updatedPots)
//         // await promptForConfirmation(team, group);  // Solicita confirmação para avançar
//
//         return group;  // Retorna o grupo onde o time foi alocado
//       }
//     }
//
//     // Se não foi possível alocar, retorna null
//     return null;
//   };
//
//   // Função para alocar os times do Pote 1 (cabeças de chave) diretamente nas primeiras posições dos grupos
//   const allocatePot1 = async () => {
//     // Embaralha os times do Pote 1 apenas uma vez
//     const shuffledPot1 = shuffle(updatedPots[1]);
//
//     // Itera sobre os grupos e aloca os times do Pote 1
//     for (const team of shuffledPot1) {
//       const index = shuffledPot1.indexOf(team);
//       const groupKeys = Object.keys(updatedGroups);
//       const group = groupKeys[index]; // Usa o índice fixo para selecionar o grupo correspondente
//
//       // Aloca o time na primeira posição do grupo
//       updatedGroups[group][0] = team;
//
//       // Atualiza os potes removendo o time alocado
//       updatedPots[1] = updatedPots[1].filter(t => t !== team);
//
//       // Loga o estado atualizado e solicita confirmação
//       logState(updatedGroups, updatedPots);
//       // await promptForConfirmation(team, group); // Solicita confirmação para avançar
//     }
//   };
//
//
//   // Função para alocar os times do Pote 2 (times da UEFA) nos grupos
//   const allocateUEFATeams = async () => {
//     const shuffledPot2 = shuffle(updatedPots[2]);
//     let uefaTeams = [...shuffledPot2];
//
//     for (let i = 0; i < uefaTeams.length; i++) {
//       const team = uefaTeams[i];
//
//       let group = null;
//
//       // Verifica se o time pode ser alocado no grupo atual (caso já tenha 1 europeu no grupo, aceita mais 1)
//       for (let groupKey of Object.keys(updatedGroups)) {
//         const uefaTeamsInGroup = updatedGroups[groupKey].filter(g => g && g.confederation === "UEFA").length;
//         if (
//             // Verifica se há uma posição vazia no grupo
//             updatedGroups[groupKey].includes(null) &&
//
//             // Permite até 2 times da UEFA por grupo
//             (team.confederation !== "UEFA" || uefaTeamsInGroup < 2) &&
//
//             // Garante que não há conflito de confederação ou país
//             updatedGroups[groupKey].every(g => {
//               if (!g) {
//                 // Posição vazia, sem conflito
//                 return true;
//               }
//               const noConflict = g.country !== team.country;
//               if (!noConflict) {
//                 console.log(`Conflito: ${team.name} (${team.confederation}, ${team.country}) não pode ser alocado no grupo ${groupKey} devido a ${g.name} (${g.confederation}, ${g.country})`);
//               }
//               return noConflict; // Retorna verdadeiro se não houver conflito
//             })
//         ) {
//           group = groupKey; // Define o grupo para o time
//           console.log(`Alocando ${team.name} (${team.confederation}, ${team.country}) no grupo ${group}`);
//           break; // Sai do loop após encontrar um grupo válido
//         }
//
//       }
//
//       if (group) {
//         const emptyIndex = updatedGroups[group].indexOf(null);
//         updatedGroups[group][emptyIndex] = team;
//         updatedPots[2] = updatedPots[2].filter(t => t !== team);
//         logState(updatedGroups, updatedPots);
//         // await promptForConfirmation(team, group);  // Solicita confirmação para avançar
//       }
//     }
//   };
//
//   // Função para alocar os times dos outros potes (Potes 3 e 4) nos grupos
//   const allocateRemainingPots = async () => {
//     // Pote 3
//     let remainingTeams = [...updatedPots[3]];
//     for (let team of remainingTeams) {
//       await assignTeamToGroup(team); // Aloca os times do Pote 3 nos grupos
//     }
//
//     // Pote 4
//     remainingTeams = [...updatedPots[4]];
//     for (let team of remainingTeams) {
//       await assignTeamToGroup(team); // Aloca os times do Pote 4 nos grupos
//     }
//   };
//   await allocatePot1(); // Aloca times do Pote 1
//   await allocateUEFATeams(); // Aloca times da UEFA do Pote 2
//   await allocateRemainingPots(); // Aloca times restantes dos Potes 3 e 4
//
//   // Emite o evento de sorteio concluído com os grupos atualizados
//   emit('sorteio-concluido', updatedGroups);
// };


const allocateTeamsFromRemainingPots = async () => {
  const updatedGroups = {...props.groups};
  const updatedPots = {...props.pots};

  // Função para embaralhar os times de cada pote
  const shuffle = (array) => {
    return array.sort(() => Math.random() - 0.5);
  };

  // Função para alocar os times do Pote 1 (cabeças de chave) diretamente nas primeiras posições dos grupos
  const allocatePot1 = async () => {
    // Embaralha os times do Pote 1 apenas uma vez
    const shuffledPot1 = shuffle(updatedPots[1]);

    // Itera sobre os grupos e aloca os times do Pote 1
    for (const team of shuffledPot1) {
      const index = shuffledPot1.indexOf(team);
      const groupKeys = Object.keys(updatedGroups);
      const group = groupKeys[index]; // Usa o índice fixo para selecionar o grupo correspondente

      // Aloca o time na primeira posição do grupo
      updatedGroups[group][0] = team;

      // Atualiza os potes removendo o time alocado
      updatedPots[1] = updatedPots[1].filter(t => t !== team);

      // Loga o estado atualizado e solicita confirmação
      logState(updatedGroups, updatedPots);
      // await promptForConfirmation(team, group); // Solicita confirmação para avançar
    }
  };

  // Função para alocar os times do Pote 2 (times da UEFA) nos grupos
  const allocateUEFATeams = async () => {
    const shuffledPot2 = shuffle(updatedPots[2]);
    let uefaTeams = [...shuffledPot2];

    for (let i = 0; i < uefaTeams.length; i++) {
      const team = uefaTeams[i];

      let group = null;

      // Verifica se o time pode ser alocado no grupo atual (caso já tenha 1 europeu no grupo, aceita mais 1)
      for (let groupKey of Object.keys(updatedGroups)) {
        const uefaTeamsInGroup = updatedGroups[groupKey].filter(g => g && g.confederation === "UEFA").length;
        if (
            // Verifica se há uma posição vazia no grupo
            updatedGroups[groupKey].includes(null) &&

            // Permite até 2 times da UEFA por grupo
            (team.confederation !== "UEFA" || uefaTeamsInGroup < 2) &&

            // Garante que não há conflito de confederação ou país
            updatedGroups[groupKey].every(g => {
              if (!g) {
                // Posição vazia, sem conflito
                return true;
              }
              const noConflict = g.country !== team.country;
              if (!noConflict) {
                console.log(`Conflito: ${team.name} (${team.confederation}, ${team.country}) não pode ser alocado no grupo ${groupKey} devido a ${g.name} (${g.confederation}, ${g.country})`);
              }
              return noConflict; // Retorna verdadeiro se não houver conflito
            })
        ) {
          group = groupKey; // Define o grupo para o time
          console.log(`Alocando ${team.name} (${team.confederation}, ${team.country}) no grupo ${group}`);
          break; // Sai do loop após encontrar um grupo válido
        }

      }

      if (group) {
        const emptyIndex = updatedGroups[group].indexOf(null);
        updatedGroups[group][emptyIndex] = team;
        updatedPots[2] = updatedPots[2].filter(t => t !== team);
        logState(updatedGroups, updatedPots);
        // await promptForConfirmation(team, group);  // Solicita confirmação para avançar
      }
    }
  };

  // Função para alocar times nos grupos, com as restrições adicionadas
  const assignTeamToGroup = async (team, startGroupIndex = 0) => {
    const groupKeys = Object.keys(updatedGroups);

    // Verificar se há necessidade de aplicar a regra para os times europeus e sul-americanos
    if (team.confederation === 'UEFA') {
      const isTopUEFATeam = ['Manchester City', 'Real Madrid', 'Bayern', 'PSG'].includes(team.name);
      if (isTopUEFATeam) {
        // Alocar contra os piores times da UEFA
        const uefaBottomTeams = ['Atlético de Madrid', 'Benfica', 'Juventus', 'RB Salzburg'];

        for (let i = startGroupIndex; i < groupKeys.length; i++) {
          const group = groupKeys[i];
          if (updatedGroups[group].some(t => uefaBottomTeams.includes(t?.name))) {
            // Aloca o time no grupo que já contém os piores times da UEFA
            const emptyIndex = updatedGroups[group].indexOf(null);
            if (emptyIndex !== -1) {
              updatedGroups[group][emptyIndex] = team;
              const potIndex = Object.keys(updatedPots).find(pot => updatedPots[pot].includes(team));
              if (potIndex) {
                updatedPots[potIndex] = updatedPots[potIndex].filter(t => t !== team);
              }
              return group;
            }
          }
        }
      } else {
        // Aloca os outros times europeus no grupo com os sul-americanos
        const southAmericanTeams = ['Flamengo', 'Palmeiras', 'River Plate', 'Fluminense'];
        for (let i = startGroupIndex; i < groupKeys.length; i++) {
          const group = groupKeys[i];
          if (updatedGroups[group].some(t => southAmericanTeams.includes(t?.name))) {
            const emptyIndex = updatedGroups[group].indexOf(null);
            if (emptyIndex !== -1) {
              updatedGroups[group][emptyIndex] = team;
              const potIndex = Object.keys(updatedPots).find(pot => updatedPots[pot].includes(team));
              if (potIndex) {
                updatedPots[potIndex] = updatedPots[potIndex].filter(t => t !== team);
              }
              return group;
            }
          }
        }
      }
    } else {
      // Aloca times não-UEFA normalmente
      for (let i = startGroupIndex; i < groupKeys.length; i++) {
        const group = groupKeys[i];
        if (updatedGroups[group].includes(null)) {
          const emptyIndex = updatedGroups[group].indexOf(null);
          updatedGroups[group][emptyIndex] = team;
          const potIndex = Object.keys(updatedPots).find(pot => updatedPots[pot].includes(team));
          if (potIndex) {
            updatedPots[potIndex] = updatedPots[potIndex].filter(t => t !== team);
          }
          return group;
        }
      }
    }

    // Caso não consiga alocar, retorna null
    return null;
  };

  // Função para alocar os times restantes nos grupos
  const allocateRemainingPots = async () => {
    // Pote 3
    let remainingTeams = [...updatedPots[3]];
    for (let team of remainingTeams) {
      await assignTeamToGroup(team); // Aloca os times do Pote 3 nos grupos
    }

    // Pote 4
    remainingTeams = [...updatedPots[4]];
    for (let team of remainingTeams) {
      await assignTeamToGroup(team); // Aloca os times do Pote 4 nos grupos
    }
  };

  // Embaralha e aloca os times do Pote 1 (cabeças de chave), Pote 2 (times da UEFA), e outros potes
  await allocatePot1(); // Aloca times do Pote 1
  await allocateUEFATeams(); // Aloca times da UEFA do Pote 2
  await allocateRemainingPots(); // Aloca times restantes dos Potes 3 e 4

  // Emite o evento de sorteio concluído com os grupos atualizados
  emit('sorteio-concluido', updatedGroups);
};


// Função para exibir o estado atual dos grupos e potes no console
const logState = (updatedGroups, updatedPots) => {
  const groupStrings = Object.keys(updatedGroups).map(group => {
    const groupString = updatedGroups[group].map(team => team ? team.name : 'Vazio').join(' | ');
    return `${group}: [${groupString}]`;
  }).join('\n');

  const potsStrings = Object.keys(updatedPots).map(pot => {
    const potString = updatedPots[pot].map(team => team.name).join(', ');
    return `Pote ${pot}: ${potString}`;
  }).join('\n');

  console.clear();
  console.log(`Grupos:\n${groupStrings}`);
  console.log(`\nTimes restantes nos potes:\n${potsStrings}`);
};

// Função para solicitar confirmação do sorteio de um time para um grupo
const promptForConfirmation = (team, group) => {
  console.log(team, group)
  return new Promise(resolve => {
    const confirmation = window.confirm(`O time sorteado ${team.name} foi alocado no Grupo ${group}. Pressione OK para continuar.`);
    resolve(confirmation);
  });
};

</script>
