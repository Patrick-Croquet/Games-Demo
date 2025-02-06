<template>
  <div class="tetris-piece" :style="pieceStyle" :data-type="type">
    <div v-for="(row, rowIndex) in shape" :key="rowIndex" class="piece-row">
      <div v-for="(cell, cellIndex) in row" 
           :key="cellIndex" 
           class="piece-cell"
           :class="{ filled: cell === 1 }"
           :style="{ backgroundColor: cell === 1 ? color : 'transparent' }">
      </div>
    </div>
  </div>
</template>

<script>
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

const COLORS = {
  I: '#00f0f0',
  O: '#f0f000',
  T: '#a000f0',
  L: '#f0a000',
  J: '#0000f0',
  S: '#00f000',
  Z: '#f00000'
};

export default {
  name: 'TetrisPiece',
  props: {
    type: {
      type: String,
      required: true,
      validator: value => ['I', 'O', 'T', 'L', 'J', 'S', 'Z'].includes(value)
    },
    rotation: {
      type: Number,
      default: 0
    },
    x: {
      type: Number,
      default: 0
    },
    y: {
      type: Number,
      default: 0
    }
  },
  computed: {
    shape() {
      if (!this.type || !SHAPES[this.type]) {
        return [[0]];
      }
      if (this.type === 'O') {
        return SHAPES[this.type].map(row => [...row]);
      }
      const baseShape = SHAPES[this.type].map(row => [...row]);
      return this.rotateShape(baseShape, this.rotation);
    },
    color() {
      return COLORS[this.type] || '#000000';
    },
    pieceStyle() {
      const style = {
        position: 'absolute',
        left: `${this.x * 30}px`,
        top: `${this.y * 30}px`,
        margin: 0,
        padding: 0
      };

      return style;
    }
  },
  methods: {
    rotateShape(shape, rotation) {
      if (this.type === 'O') return shape;

      let rotatedShape = shape.map(row => [...row]);
      for (let i = 0; i < rotation % 4; i++) {
        rotatedShape = this.rotate90Degrees(rotatedShape);
      }
      return rotatedShape;
    },
    rotate90Degrees(matrix) {
      const N = matrix.length;
      const rotated = Array(N).fill().map(() => Array(N).fill(0));
      
      for (let i = 0; i < N; i++) {
        for (let j = 0; j < N; j++) {
          rotated[j][N - 1 - i] = matrix[i][j];
        }
      }
      return rotated;
    }
  }
};
</script>

<style scoped>
.tetris-piece {
  display: flex;
  flex-direction: column;
  margin: 0;
  padding: 0;
  position: absolute;
}

.piece-row {
  display: flex;
  margin: 0;
  padding: 0;
}

.piece-cell {
  width: 30px;
  height: 30px;
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

.piece-cell.filled {
  border: 1px solid rgba(0, 0, 0, 0.2);
  box-shadow: inset 0 0 8px rgba(0, 0, 0, 0.1);
}
</style>