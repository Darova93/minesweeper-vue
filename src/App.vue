<template>
  <div>
    <BlockGrid
      ref="game"
      :difficulty="this.selectedDifficulty"
      :gridSize="this.selectedGridSize"
    />
    <button
      @click="resetGrid()"
    >
      {{ buttonText }}
    </button>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import BlockGrid from './components/BlockGrid.vue'
import { Difficulty, GridSize } from './types/GameTypes'

interface BlockGridInterface {
    generateGrid: () => void;
}

export default Vue.extend({
  name: 'App',
  components: { BlockGrid },
  data () {
    return {
      selectedDifficulty: Difficulty.Easy,
      selectedGridSize: GridSize.Large,
      currentGame: {}
    }
  },
  methods: {
    resetGrid: function (): void {
      // const game = this.$refs.game as BlockGridInterface
      // game.generateGrid()
      // const Game = Vue.extend(BlockGrid)
      // const gameInstance = new Game({
      //   propsData: {
      //     difficulty: this.selectedDifficulty,
      //     gridSize: 64
      //   }
      // })
      // this.currentGame = gameInstance.$mount()
      // const gameBox = this.$refs.game as HTMLDivElement
      // gameBox.appendChild(gameInstance.$el)
    }
  },
  computed: {
    buttonText: function (): string {
      return this.currentGame ? 'New game' : 'Restart'
    }
  }
})
</script>

<style>

</style>
