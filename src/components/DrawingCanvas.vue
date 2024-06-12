<template>
  <div class="drawing-canvas">
    <AsciiCharacterPane class="character-pane" @select-char="onSelectActiveChar" />

    <div class="canvas-container">
      <table id="canvas">
        <tbody>
          <tr v-for="(row, rowIndex) in canvas" :key="`row-${rowIndex}`" class="row">
            <td
              v-for="(cell, colIndex) in row"
              :key="`col-${colIndex}`"
              class="cell"
              @click.prevent="fillActiveChar(rowIndex, colIndex)"
              @contextmenu.prevent="fillDefaultChar(rowIndex, colIndex)"
            >
              {{ cell }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="copy">
      <button class="btn" @click="copyToClipboard">Copy to Clipboard</button>
      <Transition name="copy-msg">
        <span v-if="isVisibleCopyMsg" class="message">Copied!</span>
      </Transition>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onUnmounted } from 'vue';

import AsciiCharacterPane from './AsciiCharacterPane.vue';

const CANVAS_SIZES = { rows: 24, cols: 60 };
const DEFAULT_CHAR = ' ';

const canvas = ref(
  Array.from(Array(CANVAS_SIZES.rows), () => new Array(CANVAS_SIZES.cols).fill(DEFAULT_CHAR)),
);

const activeChar = ref(DEFAULT_CHAR);

const isVisibleCopyMsg = ref(false);

const fillActiveChar = (row: number, col: number) => {
  canvas.value[row][col] = activeChar.value;
};

const fillDefaultChar = (row: number, col: number) => {
  canvas.value[row][col] = DEFAULT_CHAR;
};

const onSelectActiveChar = (char: string) => {
  activeChar.value = char;
};

let copyMsgTimeoutId: number | undefined = undefined;

const copyToClipboard = () => {
  const artText = canvas.value.map((row) => row.join('')).join('\n');
  navigator.clipboard.writeText(artText);

  isVisibleCopyMsg.value = true;
  copyMsgTimeoutId = setTimeout(() => {
    isVisibleCopyMsg.value = false;
  }, 1500);
};

onUnmounted(() => {
  clearTimeout(copyMsgTimeoutId);
});
</script>

<style lang="scss" scoped>
.drawing-canvas {
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin: 24px 32px 0;

  .character-pane {
    min-height: 120px;
  }

  .canvas-container {
    #canvas {
      $cell-width: 12px;
      $cell-height: 16px;

      table-layout: fixed;
      width: calc(#{$cell-width} * 60);
      border-collapse: collapse;
      margin-top: 48px;

      .cell {
        border: 1px solid lightgray;
        width: $cell-width;
        height: $cell-height;
        padding: 0;
        text-align: center;
        cursor: pointer;
        font-size: $cell-width;
        line-height: 1;
      }
    }
  }

  .copy {
    .btn {
      width: 200px;
      padding: 4px;
      margin: 24px 0;
      background-color: gray;
      color: white;
      font-size: 18px;
      border: 1px solid lightgray;
      border-radius: 4px;
      opacity: 0.5;

      &:hover {
        opacity: 0.8;
        box-shadow: 0 0 2px rgba(black, 0.8);
      }
    }

    .message {
      margin-left: 8px;
      font-size: 16px;
    }
  }
}

.copy-msg-active {
  transform: translate(0px, 0px);
  transition: transform 700ms ease-in 0ms;
}

.copy-msg-enter-from {
  transform: translateY(100vh) translateY(0px);
}
</style>
