<template>
  <div
    id="mine-id"
    :class="['block-square', revealed ? 'revealed' : '']"
    @click.left="() => revealBlock()"
    @click.right="(e) => markBlock(e)"
  >
    <img
        class="block-image"
        v-if="imageSrc"
        :src="imageSrc">
    <span
        class="block-number"
        v-if="revealed && blockValue !== -1">
        {{ blockValue > 0 ? blockValue : '' }}
    </span>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

export default Vue.extend({
  name: 'Block',
  props: {
    blockValue: {
      type: Number,
      required: true
    },
    rowIndex: {
      type: Number,
      required: true
    },
    colIndex: {
      type: Number,
      required: true
    },
    revealed: {
      type: Boolean,
      required: true
    }
  },
  data () {
    return {
      marked: false,
      imageSrc: '',
      mineSrc: require('../assets/mine.png'),
      flagSrc: require('../assets/flag.png')
    }
  },
  methods: {
    revealBlock: function (): void {
      if (!this.marked) {
        this.imageSrc = this.blockValue === -1 ? this.mineSrc : ''
        this.$emit('revealBlock', this.rowIndex, this.colIndex)
      }
    },
    markBlock: function (e: Event): void {
      e.preventDefault()
      e.stopPropagation()
      if (!this.revealed) {
        this.marked = !this.marked
        this.imageSrc = this.marked ? this.flagSrc : ''
      }
    }
  }
})
</script>

<style lang="scss">
$mine-size: 25px;
$mine-color: gray;

.block {
  &-square {
    background-color: $mine-color;
    border-radius: 4px;
    display: flex;
    align-items: center;
    justify-content: center;
    width: $mine-size;
    height: $mine-size;
  }
  &-image {
    width: $mine-size;
    height: $mine-size;
  }
  &-number {
    color: white;
  }
}
.revealed {
  background-color: lightgray;
  box-shadow: inset 0px 0px 4px 1px gray;
}
</style>
