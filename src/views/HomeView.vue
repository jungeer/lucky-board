<template>
  <div class="grid-container" ref="gridContainerRef">
    <div
      v-for="(cell, index) in gridData"
      :key="index"
      :style="{
        backgroundColor: colors[index],
        width: cellWidths[index] + 'px',
        height: cellHeights[index] + 'px',
      }"
      class="grid-cell"
    >
      {{ texts[index] }}
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

import luckyColors from "./colors";

import luckyTexts from "./lucky-texts";

// 预定义的颜色数组
const colors = ref(luckyColors);

// 预定义的幸运文本数组
const texts = ref(luckyTexts);

const gridData = Array.from({ length: 64 }).fill(null);

const gridContainerRef = ref(null);
const cellWidths = ref(Array(64).fill(0));
const cellHeights = ref(Array(64).fill(0));

const updateGrid = () => {
  const gridContainer = gridContainerRef.value;
  const containerWidth = gridContainer.offsetWidth;
  const containerHeight = gridContainer.offsetHeight;
  const totalCells = 64;

  // 计算单元格的行数和列数
  const aspectRatio = containerWidth / containerHeight;
  let cols = Math.ceil(Math.sqrt(totalCells * aspectRatio));
  let rows = Math.ceil(totalCells / cols);

  // 调整行数或列数以确保总格子数为64
  while (cols * rows > totalCells) {
    if ((cols - 1) * rows >= totalCells) {
      cols--;
    } else {
      rows--;
    }
  }

  // 计算单元格的宽度和高度
  const cellWidth = containerWidth / cols;
  const cellHeight = containerHeight / rows;

  // 更新每个单元格的宽度和高度
  for (let i = 0; i < totalCells; i++) {
    const colIndex = i % cols;
    const rowIndex = Math.floor(i / cols);
    cellWidths.value[i] =
      colIndex === cols - 1 ? containerWidth - colIndex * cellWidth : cellWidth;
    cellHeights.value[i] =
      rowIndex === rows - 1
        ? containerHeight - rowIndex * cellHeight
        : cellHeight;
  }
};

onMounted(() => {
  updateGrid();
  window.addEventListener("resize", updateGrid);
});

onUnmounted(() => {
  window.removeEventListener("resize", updateGrid);
});
</script>

<style>
.grid-container {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  height: 100%;
}

.grid-cell {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
