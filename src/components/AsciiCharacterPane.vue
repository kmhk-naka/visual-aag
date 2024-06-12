<template>
  <div class="ascii-character-pane">
    <div class="tab-list">
      <span
        class="label"
        v-for="tab in tabList"
        :key="tab.key"
        :selected="tab.key === selectedTabKey"
        @click="onSelectTab(tab.key)"
        >{{ tab.name }}</span
      >
    </div>

    <div class="character-list">
      <button
        v-for="char in asciiChars[selectedTabKey]"
        :key="`char-${char}`"
        class="cell"
        :class="{ selected: char === selectedChar }"
        @click="onSelectChar(char)"
      >
        {{ char }}
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

type TabKey = 'az' | 'AZ' | 'number' | 'symbol';

const emit = defineEmits<{
  'select-char': [char: string];
}>();

const asciiChars: Record<TabKey, string[]> = {
  az: 'abcdefghijklmnopqrstuvwxyz'.split(''),
  AZ: 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split(''),
  number: '0123456789'.split(''),
  symbol: '!"#$%&\'()*+,-./:;<=>?@[\\]^_`{|}~'.split(''),
};

const tabList: { key: TabKey, name: string }[] = [
  { key: 'az', name: 'a-z' },
  { key: 'AZ', name: 'A-Z' },
  { key: 'number', name: '0-9' },
  { key: 'symbol', name: 'Symbol' },
];

const selectedTabKey = ref<TabKey>(tabList[0].key);

const selectedChar = ref(' ');

const onSelectChar = (char: string) => {
  selectedChar.value = char;
  emit('select-char', char);
};

const onSelectTab = (key: TabKey) => {
  selectedTabKey.value = key;
};
</script>

<style lang="scss" scoped>
.ascii-character-pane {
  width: 600px;

  .tab-list {
    display: flex;
    flex-wrap: wrap;
    gap: 0 10px;

    .label {
      order: -1;
      opacity: 0.5;
      min-width: 70px;
      padding: 0.6em 1em;
      border-radius: 5px 5px 0 0;
      background-color: gray;
      color: #fff;
      font-size: 0.9em;
      text-align: center;
      cursor: pointer;

      &:hover {
        opacity: 0.8;
      }

      &[selected='true'] {
        opacity: 1;
      }
    }
  }

  .character-list {
    $cell-size: 24px;

    display: grid;
    grid-template-columns: repeat(auto-fill, minmax($cell-size, 1fr));
    row-gap: 4px;
    column-gap: 4px;
    padding: 16px 8px 8px;
    border: 2px solid gray;

    .cell {
      width: $cell-size;
      height: $cell-size;
      padding: 4px;
      box-sizing: border-box;
      border: 1px solid gray;
      border-radius: 2px;
      background-color: whitesmoke;
      cursor: pointer;

      &.selected {
        $selected-color: rgb(0, 119, 255);

        border: 1px solid $selected-color;
        background-color: white;
        box-shadow: 0 0 2px rgba($selected-color, 0.8);
      }
    }
  }
}
</style>
