<template>
  <div class="tetris-container" tabindex="0" @keydown.prevent="handleKeyPress" ref="container">
    <div class="game-info">
      <div class="score">Score: {{ score }}</div>
      <div class="level">Niveau: {{ level }}</div>
      <div class="next-piece">
        <h3>Prochaine pièce:</h3>
        <div class="next-piece-container">
          <tetris-piece
            :type="nextPieceType"
            :x="0"
            :y="0"
            :rotation="0"
            class="preview-piece"
          />
        </div>
      </div>
      <div class="controls">
        <button @click="togglePause">{{ isPaused ? 'Reprendre' : 'Pause' }}</button>
        <div class="controls-info">
          <h3>Contrôles:</h3>
          <ul>
            <li>← → : Déplacer</li>
            <li>↑ : Rotation</li>
            <li>↓ : Descente rapide</li>
            <li>Espace : Chute instantanée</li>
            <li>P : Pause</li>
          </ul>
        </div>
      </div>
    </div>
    <div class="tetris-grid">
      <div
        v-for="(row, rowIndex) in grid"
        :key="rowIndex"
        class="tetris-row"
      >
        <div
          v-for="(cell, cellIndex) in row"
          :key="cellIndex"
          class="tetris-cell"
            :style="getCellStyle(cell)"
        ></div>
      </div>
      <tetris-piece
        v-if="currentPiece && !isPaused"
        ref="currentPiece"
        :type="currentPiece.type"
        :x="currentPiece.x"
        :y="currentPiece.y"
        :rotation="currentPiece.rotation"
      />
      <div v-if="isPaused" class="pause-screen">
        <h2>Pause</h2>
        <p>Appuyez sur P pour reprendre</p>
      </div>
    </div>
    <div v-if="gameOver" class="game-over">
      <h2>Game Over!</h2>
      <button @click="startGame">Nouvelle partie</button>
    </div>
  </div>
</template>

<script>
import TetrisPiece from './TetrisPiece.vue';

const COLORS = {
  I: '#00f0f0',
  O: '#f0f000',
  T: '#a000f0',
  L: '#f0a000',
  J: '#0000f0',
  S: '#00f000',
  Z: '#f00000'
};

const SHAPES = {
  I: [
    [0, 0, 0, 0],
    [1, 1, 1, 1],
    [0, 0, 0, 0],
    [0, 0, 0, 0]
  ],
  O: [
    [1, 1],
    [1, 1]
  ],
  T: [
    [0, 1, 0],
    [1, 1, 1],
    [0, 0, 0]
  ],
  L: [
    [1, 0, 0],
    [1, 1, 1],
    [0, 0, 0]
  ],
  J: [
    [0, 0, 1],
    [1, 1, 1],
    [0, 0, 0]
  ],
  S: [
    [0, 1, 1],
    [1, 1, 0],
    [0, 0, 0]
  ],
  Z: [
    [1, 1, 0],
    [0, 1, 1],
    [0, 0, 0]
  ]
};

export default {
  components: {
    TetrisPiece
  },
  data() {
    return {
      grid: [],
      currentPiece: null,
      nextPieceType: null,
      gameInterval: null,
      score: 0,
      level: 1,
      gameOver: false,
      isPaused: false,
      pieceTypes: ['I', 'O', 'T', 'L', 'J', 'S', 'Z']
    };
  },
  mounted() {
    this.startGame();
    this.$refs.container.focus();
  },
  beforeUnmount() {
    this.stopGame();
  },
  methods: {
    startGame() {
      this.grid = this.createGrid(20, 10);
      this.score = 0;
      this.level = 1;
      this.gameOver = false;
      this.isPaused = false;
      this.nextPieceType = this.getRandomPieceType();
      this.spawnNewPiece();
      this.startGameLoop();
    },
    stopGame() {
      if (this.gameInterval) {
        clearInterval(this.gameInterval);
      }
    },
    createGrid(rows, cols) {
      return Array.from({ length: rows }, () => Array(cols).fill(null));
    },
    getRandomPieceType() {
      return this.pieceTypes[Math.floor(Math.random() * this.pieceTypes.length)];
    },
    spawnNewPiece() {
      this.currentPiece = {
        type: this.nextPieceType,
        x: 4,
        y: 0,
        rotation: 0
      };
      this.nextPieceType = this.getRandomPieceType();
      
      if (this.checkCollision(this.currentPiece)) {
        this.gameOver = true;
        this.stopGame();
      }
    },
    startGameLoop() {
      const baseSpeed = 1000;
      const speed = baseSpeed - (this.level - 1) * 50;
      this.gameInterval = setInterval(() => {
        this.moveDown();
      }, Math.max(speed, 100));
    },
    togglePause() {
      this.isPaused = !this.isPaused;
      if (this.isPaused) {
        clearInterval(this.gameInterval);
      } else {
        this.startGameLoop();
        this.$refs.container.focus();
      }
    },
    handleKeyPress(event) {
      if (this.gameOver) return;
      
      switch(event.key) {
        case 'ArrowLeft':
          if (!this.isPaused) this.moveHorizontal(-1);
          break;
        case 'ArrowRight':
          if (!this.isPaused) this.moveHorizontal(1);
          break;
        case 'ArrowDown':
          if (!this.isPaused) this.moveDown();
          break;
        case 'ArrowUp':
          if (!this.isPaused) this.rotate();
          break;
        case ' ':
          if (!this.isPaused) this.hardDrop();
          break;
        case 'p':
        case 'P':
          this.togglePause();
          break;
      }
    },
    moveHorizontal(direction) {
      const newPiece = {
        ...this.currentPiece,
        x: this.currentPiece.x + direction
      };
      
      if (!this.checkCollision(newPiece)) {
        this.currentPiece = newPiece;
      }
    },
    moveDown() {
      const newPiece = {
        ...this.currentPiece,
        y: this.currentPiece.y + 1
      };
      
      if (!this.checkCollision(newPiece)) {
        this.currentPiece = newPiece;
      } else {
        this.lockPiece();
        this.clearLines();
        this.spawnNewPiece();
      }
    },
    rotate() {
      if (!this.currentPiece) return;

      const newRotation = (this.currentPiece.rotation + 1) % 4;
      const newPiece = {
        ...this.currentPiece,
        rotation: newRotation
      };

      // Essayer la rotation normale
      if (!this.checkCollision(newPiece)) {
        this.currentPiece = newPiece;
        return;
      }

      // Wall kicks pour différentes pièces
      const kicks = this.currentPiece.type === 'I' 
        ? [
            { x: -2, y: 0 }, { x: 2, y: 0 },   // Kicks horizontaux larges pour la pièce I
            { x: -1, y: 0 }, { x: 1, y: 0 },   // Kicks horizontaux standards
            { x: 0, y: -1 }, { x: 0, y: 1 },   // Kicks verticaux
            { x: -2, y: -1 }, { x: 2, y: -1 }, // Kicks diagonaux
          ]
        : [
            { x: -1, y: 0 }, { x: 1, y: 0 },   // Kicks horizontaux
            { x: 0, y: -1 },                    // Kick vers le haut
            { x: -1, y: -1 }, { x: 1, y: -1 }, // Kicks diagonaux
          ];

      // Essayer chaque wall kick
      for (const kick of kicks) {
        const kickedPiece = {
          ...newPiece,
          x: newPiece.x + kick.x,
          y: newPiece.y + kick.y
        };

        if (!this.checkCollision(kickedPiece)) {
          this.currentPiece = kickedPiece;
          return;
        }
      }

      // Si aucun kick ne fonctionne, essayer de revenir à la rotation précédente
      const previousRotation = (this.currentPiece.rotation + 3) % 4;
      this.currentPiece = {
        ...this.currentPiece,
        rotation: previousRotation
      };
    },
    hardDrop() {
      while (!this.checkCollision({
        ...this.currentPiece,
        y: this.currentPiece.y + 1
      })) {
        this.currentPiece.y++;
      }
      this.lockPiece();
      this.clearLines();
      this.spawnNewPiece();
    },
    checkCollision(piece) {
      if (!this.$refs.currentPiece || !piece) return false;
      
      // On obtient la forme après rotation
      const rotatedShape = this.getRotatedShape(piece);
      if (!rotatedShape) return false;
      
      for (let y = 0; y < rotatedShape.length; y++) {
        for (let x = 0; x < rotatedShape[y].length; x++) {
          if (rotatedShape[y][x]) {
            const newX = piece.x + x;
            const newY = piece.y + y;
            
            // Vérification des limites de la grille
            if (newX < 0 || newX >= this.grid[0].length || newY >= this.grid.length) {
              return true;
            }

            // Vérification des collisions avec les pièces existantes
            if (newY >= 0 && this.grid[newY][newX]) {
              return true;
            }
          }
        }
      }
      return false;
    },
    getRotatedShape(piece) {
      const shape = SHAPES[piece.type];
      if (!shape) return null;

      // Si c'est une pièce O, pas besoin de rotation
      if (piece.type === 'O') {
        return shape;
      }

      // Copier la forme
      let rotatedShape = shape.map(row => [...row]);
      
      // Appliquer la rotation
      for (let i = 0; i < piece.rotation % 4; i++) {
        const N = rotatedShape.length;
        const newShape = Array(N).fill().map(() => Array(N).fill(0));
        
        for (let y = 0; y < N; y++) {
          for (let x = 0; x < N; x++) {
            newShape[x][N - 1 - y] = rotatedShape[y][x];
          }
        }
        rotatedShape = newShape;
      }
      
      return rotatedShape;
    },
    lockPiece() {
      if (!this.$refs.currentPiece || !this.currentPiece) return;
      const shape = this.$refs.currentPiece.shape;
      if (!shape) return;
      
      for (let y = 0; y < shape.length; y++) {
        for (let x = 0; x < shape[y].length; x++) {
          if (shape[y][x]) {
            const gridY = this.currentPiece.y + y;
            const gridX = this.currentPiece.x + x;
            if (gridY >= 0) {
              this.grid[gridY][gridX] = this.currentPiece.type;
            }
          }
        }
      }
    },
    clearLines() {
      let linesCleared = 0;
      
      for (let y = this.grid.length - 1; y >= 0; y--) {
        if (this.grid[y].every(cell => cell !== null)) {
          this.grid.splice(y, 1);
          this.grid.unshift(Array(this.grid[0].length).fill(null));
          linesCleared++;
          y++;
        }
      }
      
      if (linesCleared > 0) {
        this.updateScore(linesCleared);
      }
    },
    updateScore(linesCleared) {
      const points = [0, 100, 300, 500, 800];
      this.score += points[linesCleared] * this.level;
      this.level = Math.floor(this.score / 1000) + 1;
      
      if (this.gameInterval) {
        clearInterval(this.gameInterval);
        this.startGameLoop();
      }
    },
    getCellStyle(cell) {
      if (!cell) return {};
      return {
        backgroundColor: COLORS[cell]
      };
    }
  }
};
</script>

<style scoped>
.tetris-container {
  display: flex;
  justify-content: center;
  align-items: flex-start;
  gap: 2rem;
  padding: 2rem;
  outline: none;
  user-select: none;
}

.game-info {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  width: 200px;
}

.tetris-grid {
  display: flex;
  flex-direction: column;
  border: 2px solid #000;
  position: relative;
  background-color: #f0f0f0;
  padding: 0;
  margin: 0;
  overflow: hidden;
}

.tetris-row {
  display: flex;
  margin: 0;
  padding: 0;
}

.tetris-cell {
  width: 30px;
  height: 30px;
  border: 1px solid #ccc;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.next-piece {
  border: 1px solid #ddd;
  padding: 1rem;
  background: #f5f5f5;
  border-radius: 4px;
  width: 100%;
  box-sizing: border-box;
}

.next-piece h3 {
  margin: 0 0 1rem 0;
  color: #2c3e50;
  font-size: 1.1rem;
}

.next-piece-container {
  position: relative;
  width: 100%;
  height: 120px;
  display: flex;
  justify-content: center;
  align-items: center;
  background: white;
  border-radius: 4px;
  border: 1px solid #ddd;
  overflow: hidden;
}

.preview-piece {
  position: absolute !important;
  transform: translate(-50%, -50%) !important;
  left: 50% !important;
  top: 50% !important;
}

/* Ajustement spécifique pour la pièce I qui est plus large */
.preview-piece[data-type="I"] {
  transform: translate(-50%, -50%) translateY(15px) !important;
}

/* Ajustement spécifique pour la pièce O qui est plus petite */
.preview-piece[data-type="O"] {
  transform: translate(-50%, -50%) scale(1.2) !important;
}

.game-over {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: rgba(0, 0, 0, 0.8);
  color: white;
  padding: 2rem;
  text-align: center;
  border-radius: 8px;
  z-index: 10;
}

.game-over button {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  font-size: 1.2rem;
  cursor: pointer;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
}

.score, .level {
  font-size: 1.5rem;
  font-weight: bold;
  padding: 0.5rem;
  background: #f5f5f5;
  border-radius: 4px;
  border: 1px solid #ddd;
}

.controls {
  margin-top: 1rem;
  display: flex;
  flex-direction: column;
  gap: 1rem;
  width: 100%;
}

.controls button {
  padding: 0.5rem 1rem;
  font-size: 1.2rem;
  cursor: pointer;
  background: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  width: 100%;
}

.controls-info {
  text-align: left;
  background: #f5f5f5;
  padding: 1rem;
  border-radius: 4px;
  border: 1px solid #ddd;
}

.controls-info h3 {
  margin: 0 0 0.5rem 0;
  font-size: 1.1rem;
  color: #2c3e50;
}

.controls-info ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.controls-info li {
  margin: 0.3rem 0;
  font-size: 0.9rem;
  color: #34495e;
}

.pause-screen {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.8);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: white;
  z-index: 5;
}

.pause-screen h2 {
  font-size: 2rem;
  margin-bottom: 1rem;
}

.pause-screen p {
  font-size: 1.2rem;
}

.tetris-container:focus {
  outline: none;
}

.tetris-container:focus-visible {
  outline: 2px solid #4CAF50;
  border-radius: 4px;
}
</style>