<template>
  <div class="block-grid">
    <div
      class="block-grid__row"
      :key="rowInd"
      v-for="(row, rowInd) in valueGrid">
      <Block
        class="block-grid__element"
        :key="colInd"
        v-for="(block, colInd) in row"
        :blockValue="block"
        :rowIndex="rowInd"
        :colIndex="colInd"
        :revealed="revealedGrid[rowInd][colInd]"
        @revealBlock="(row, col) => handleReveal(row, col)"
      />
    </div>
  </div>
</template>

<script lang="ts">
import Vue, { PropType } from 'vue'
import Block from './Block.vue'
import { Difficulty, GridSize } from '../types/GameTypes'

export default Vue.extend({
  name: 'BlockGrid',
  components: { Block },
  props: {
    difficulty: {
      type: Number as PropType<Difficulty>,
      required: true
    },
    gridSize: {
      type: Number as PropType<GridSize>,
      required: true
    }
  },
  data () {
    return {
      valueGrid: [] as Array<Array<number>>,
      revealedGrid: [] as Array<Array<boolean>>,
      mineList: [] as Array<Array<number>>,
      audio: new Audio(require('../assets/click.ogg')),
      girdSizes: [
        [8, 8],
        [10, 14],
        [12, 16]
      ] as Array<Array<number>>
    }
  },
  mounted () {
    this.generateGrid()
  },
  methods: {
    generateGrid: function (): void {
      this.valueGrid = [] as Array<Array<number>>
      this.mineList = [] as Array<Array<number>>
      const numOfRows = this.girdSizes[this.gridSize][0] as number
      const numOfCols = this.girdSizes[this.gridSize][1] as number
      const numOfMines = (this.difficulty * numOfRows * numOfCols) as number
      while (this.mineList.length < numOfMines) {
        const newMine = [Math.floor(Math.random() * numOfRows), Math.floor(Math.random() * numOfCols)]
        if (!this.mineList.some((mine) => mine[0] === newMine[0] && mine[1] === newMine[1])) {
          this.mineList.push(newMine)
        }
      }
      this.revealedGrid = []
      for (let rowInd = 0; rowInd < numOfRows; rowInd++) {
        const row = [] as Array<number>
        const revealRow = [] as Array<boolean>
        for (let colInd = 0; colInd < numOfCols; colInd++) {
          row.push(this.blockValue(rowInd, colInd))
          revealRow.push(false)
        }
        this.valueGrid.push(row)
        this.revealedGrid.push(revealRow)
      }
    },
    blockValue: function (row: number, col: number): number {
      let mines = 0
      for (let i = 0; i < this.mineList.length; i++) {
        if (this.mineList[i][0] === row && this.mineList[i][1] === col) {
          return -1
        }
        const absDist = [Math.abs(this.mineList[i][0] - row), Math.abs(this.mineList[i][1] - col)]
        if ((absDist[0] === 0 && absDist[1] === 1) ||
            (absDist[0] === 1 && absDist[1] === 0) ||
            (absDist[0] === 1 && absDist[1] === 1)) {
          mines++
        }
      }
      return mines
    },
    handleReveal: function (row: number, col: number): void {
      const newRow = this.revealedGrid[row]
      newRow[col] = true
      this.$set(this.revealedGrid, row, newRow)
      this.audio.play()
      if (this.valueGrid[row][col] === 0) {
        this.triggerBlanks(row, col)
      }
    },
    triggerBlanks: function (row: number, col: number): void {
      for (let rowInd = Math.max(row - 1, 0); rowInd <= Math.min(row + 1, this.valueGrid.length - 1); rowInd++) {
        for (let colInd = Math.max(col - 1, 0); colInd <= Math.min(col + 1, this.valueGrid[row].length - 1); colInd++) {
          if (!this.revealedGrid[rowInd][colInd]) {
            const newRow = this.revealedGrid[rowInd]
            newRow[colInd] = true
            this.$set(this.revealedGrid, rowInd, newRow)
            if (this.valueGrid[rowInd][colInd] === 0) {
              this.triggerBlanks(rowInd, colInd)
            }
          }
        }
      }
    }
  }
})
</script>

<style lang="scss">
.block-grid {
  display: flex;
  flex-flow: column;
  &__row {
    display: flex;
    flex-direction: row;
  }
  &__element {
    margin: 2px;
    cursor: pointer;
  }
}
</style>
