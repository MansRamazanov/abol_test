<template>
  <div
    ref="scrollContainer"
    class="scroll-container"
    @scroll="updateVisibleRows"
  >
    <div :style="{ height: totalHeight + 'px', position: 'relative' }">
      <div
        v-for="(row, rowIndex) in visibleRows"
        :key="rowIndex + startIndex"
        :style="{
          transform: `translateY(${(rowIndex + startIndex) * itemHeight}px)`,
        }"
        class="grid_row"
      >
        <div
          v-for="(square, squareIndex) in row"
          :key="squareIndex"
          :style="{
            width: '50px',
            height: '50px',
            border: '2px solid black',
            borderRadius: `${square.borderRadius}px`,
          }"
          class="grid_item"
        >
          {{ square.value }}
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

const data = ref([]);
const visibleRows = ref([]);
const scrollContainer = ref(null);

const itemHeight = 55;
const buffer = 10;

const startIndex = ref(0);
const endIndex = ref(0);
const totalHeight = ref(0);

const updateVisibleRows = () => {
  const container = scrollContainer.value;
  if (container) {
    const scrollTop = container.scrollTop;
    const viewportHeight = container.clientHeight;

    startIndex.value = Math.max(0, Math.floor(scrollTop / itemHeight) - buffer);
    endIndex.value = Math.min(
      data.value.length,
      Math.ceil((scrollTop + viewportHeight) / itemHeight) + buffer
    );

    visibleRows.value = data.value.slice(startIndex.value, endIndex.value);
  }
};

onMounted(() => {
 
  for (let i = 0; i < 100000; i++) {
    const squares = [];
    const numSquares = Math.floor(Math.random() * 11) + 10;
    console.log(numSquares);

    for (let j = 0; j < numSquares; j++) {
      const borderRadius = Math.floor(Math.random() * 51);
      const value = Math.floor(Math.random() * 101);
      squares.push({ borderRadius, value });
    }

    data.value.push(squares);
  }

  totalHeight.value = data.value.length * itemHeight;
  updateVisibleRows();

  scrollContainer.value.addEventListener('scroll', updateVisibleRows);
});

onBeforeUnmount(() => {
  scrollContainer.value.removeEventListener('scroll', updateVisibleRows);
});
</script>

<style scoped lang="scss">
.scroll-container {
  height: 100vh;
  overflow-y: scroll;
}

.grid_row {
  display: flex;
  flex-direction: row;
  width: 100%;
  position: absolute;
}

.grid_item {
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 800;
  margin: 5px;
  transition: 0.3s cubic-bezier(0.17, 0.67, 0.83, 0.67);

  &:hover {
    transform: scale(0.8);
    transition: 0.3s cubic-bezier(0.17, 0.67, 0.83, 0.67);
    cursor: pointer;
  }
}
</style>
