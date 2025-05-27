<template>
  <div class="ttt-container">
    <div class="ttt-title">Tic Tac Toe Classic</div>
    <div class="ttt-status" :style="{ color: isGameOver ? colors.accent : colors.secondary }">
      <template v-if="winner !== null">
        {{ winner === 'Draw' ? "It's a Draw!" : `Player ${winner} wins!` }}
      </template>
      <template v-else>
        Player <span :style="{ color: colors.accent }">{{ currentPlayer }}</span>'s turn
      </template>
    </div>
    <div class="ttt-board">
      <div
        v-for="(cell, idx) in board"
        :key="idx"
        class="ttt-cell"
        :class="{ filled: cell, win: isWinningCell(idx) }"
        @click="handleCellClick(idx)"
      >
        <span
          :style="{
            color: cell === 'X' ? colors.secondary : cell === 'O' ? colors.accent : ''
          }"
        >{{ cell }}</span>
      </div>
    </div>
    <button class="ttt-reset" @click="resetGame">Restart Game</button>
  </div>
</template>

<script setup lang="ts">
// PUBLIC_INTERFACE
/**
 * Main container for TicTacToe Classic
 * Features: two player mode, win/draw detection, restart game, minimalist light UI.
 */
import { ref, computed } from 'vue'

const colors = {
  primary: '#ffffff',
  secondary: '#000000',
  accent: '#2196f3'
}

// 3x3 board, values: 'X' | 'O' | ''
const board = ref(Array(9).fill(''))
const currentPlayer = ref('X')
const winner = ref(null) // 'X', 'O', or 'Draw'
const winningLine = ref([]) // indexes of winning cells (for highlight)

function checkWinner(b) {
  const wins = [
    [0,1,2],[3,4,5],[6,7,8], // rows
    [0,3,6],[1,4,7],[2,5,8], // cols
    [0,4,8],[2,4,6]          // diagonals
  ]
  for (const line of wins) {
    const [a, bIdx, c] = line
    if (b[a] && b[a] === b[bIdx] && b[a] === b[c]) {
      winningLine.value = line
      return b[a]
    }
  }
  if (b.every(val => val)) return 'Draw'
  return null
}

function handleCellClick(idx) {
  if (winner.value || board.value[idx]) return
  board.value[idx] = currentPlayer.value
  const r = checkWinner(board.value)
  if (r) {
    winner.value = r
  } else {
    currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
  }
}

function resetGame() {
  board.value = Array(9).fill('')
  currentPlayer.value = 'X'
  winner.value = null
  winningLine.value = []
}

const isGameOver = computed(() => winner.value !== null)
function isWinningCell(idx) {
  return winningLine.value.includes(idx)
}
</script>

<style scoped>
.ttt-container {
  background: #fff;
  border-radius: 22px;
  min-width: 320px;
  max-width: 360px;
  margin: 60px auto;
  padding: 2.5rem 1.75rem 2rem;
  box-shadow: 0 4px 32px rgba(33,33,33,0.07);
  display: flex;
  flex-direction: column;
  align-items: center;
}
.ttt-title {
  font-size: 1.35rem;
  font-weight: 700;
  letter-spacing: 0.11em;
  margin-bottom: 1.65rem;
  color: #000;
  text-align: center;
}
.ttt-status {
  font-size: 1.07rem;
  margin-bottom: 1.15rem;
  min-height: 1.5em;
  letter-spacing: 0.03em;
}
.ttt-board {
  display: grid;
  grid-template-columns: repeat(3, 56px);
  grid-template-rows: repeat(3, 56px);
  gap: 8px;
  margin-bottom: 1.4rem;
}
.ttt-cell {
  background: #fff;
  border: 1.4px solid #dbe4ec;
  border-radius: 9px;
  font-size: 2rem;
  font-weight: 500;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: border-color 0.2s, box-shadow 0.2s;
  cursor: pointer;
  min-width: 0;
  min-height: 0;
  height: 56px;
  width: 56px;
  user-select: none;
}
.ttt-cell.filled {
  cursor: default;
}
.ttt-cell.win {
  border-color: #2196f3;
  box-shadow: 0 0 0 3px #2196f340;
}
.ttt-cell:hover:not(.filled):not(.win) {
  border-color: #2196f3;
  background: #eaf4fd;
}
.ttt-reset {
  background: #2196f3;
  color: #fff;
  border: none;
  font-size: 1rem;
  padding: 0.52em 1.82em;
  border-radius: 24px;
  margin-top: 0.5rem;
  box-shadow: 0 2px 8px rgba(33,150,243,0.04);
  cursor: pointer;
  transition: background 0.13s;
}
.ttt-reset:hover, .ttt-reset:focus {
  background: #1567ba;
}

@media (max-width: 400px) {
  .ttt-container {
    padding: 1.2rem 0.3rem 1.2rem;
    min-width: 0;
    max-width: 98vw;
  }
  .ttt-board {
    grid-template-columns: repeat(3, 42px);
    grid-template-rows: repeat(3, 42px);
    gap: 5px;
  }
  .ttt-cell {
    font-size: 1.28rem;
    height: 42px;
    width: 42px;
  }
}
</style>
