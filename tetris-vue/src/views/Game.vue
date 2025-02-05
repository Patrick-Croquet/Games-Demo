<template>
  <div class="game">
    <TetrisGrid :grid="grid" />
    <TetrisPiece :piece="currentPiece" />
  </div>
</template>

<script>
import TetrisGrid from '../components/TetrisGrid.vue';
import TetrisPiece from '../components/TetrisPiece.vue';

export default {
  components: {
    TetrisGrid,
    TetrisPiece
  },
  data() {
    return {
      grid: this.createEmptyGrid(),
      currentPiece: this.spawnPiece()
    };
  },
  methods: {
    createEmptyGrid() {
      return Array.from({ length: 20 }, () => Array(10).fill(0));
    },
    spawnPiece() {
      // Logique pour générer une nouvelle pièce
      return {
        shape: [[1, 1, 1], [0, 1, 0]], // Exemple de forme
        position: { x: 4, y: 0 }
      };
    },
    movePiece(direction) {
      // Logique pour déplacer la pièce
    },
    rotatePiece() {
      // Logique pour faire pivoter la pièce
    },
    updateGrid() {
      // Logique pour mettre à jour la grille
    },
    checkCompleteLines() {
      // Logique pour vérifier les lignes complètes
    }
  },
  mounted() {
    // Logique pour démarrer le jeu
  }
};
</script>

<style scoped>
.game {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>