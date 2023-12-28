<template>
  <div class="grid-container" ref="gridContainerRef">
    <template v-for="(cell, index) in gridData" :key="index">
      <div
        :style="{
          width: cellWidths[index] + 'px',
          height: cellHeights[index] + 'px',
        }"
        class="grid-cell"
        @click="flipCard(index)"
        :class="{ 'no-click': isFlipping && index !== currentIndex }"
      >
        <div class="card">
          <div class="card-inner" :class="{ flipped: flippedCards[index] }">
            <div class="card-front" :style="{ backgroundColor: colors[index] }">
            </div>
            <div class="card-back" :style="{ backgroundColor: colors[index] }">
            </div>
          </div>
        </div>
      </div>
    </template> 
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

import { gsap } from "gsap";

import { Fireworks } from 'fireworks-js'

import luckyColors from "../js/colors";

import luckyTexts from "../js/lucky-texts";

// 预定义的颜色数组
const colors = ref(luckyColors);

// 预定义的幸运文本数组
const texts = ref(luckyTexts);

const gridData = Array.from({ length: 64 }).fill(null);

const gridContainerRef = ref(null);
const cellWidths = ref(Array(64).fill(0));
const cellHeights = ref(Array(64).fill(0));

const fireworks = ref(null);

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

  fireworks.value = new Fireworks(gridContainerRef.value, { /* options */ })
};

let isFlipping = false;
let currentIndex = -1;
const flippedCards = ref(Array(64).fill(false));




const flipCard = async (index) => {
  if (!isFlipping) {
    console.log("fire: ", fireworks.value)

    fireworks.value.start()

    isFlipping = true;
    currentIndex = index;

    const cardInner =
      gridContainerRef.value.querySelectorAll(".card-inner")[index];
    gsap.to(cardInner, {
      rotateY: "+=180",
      duration: 0.5,
      onComplete: () => {
        flippedCards.value[index] = !flippedCards.value[index];
        gsap.set(cardInner, { rotateY: "0" });
        isFlipping = false;
      },
    });
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
  cursor: pointer;
  &.no-click {
    cursor: not-allowed;
  }
}

.card {
  backface-visibility: hidden;
  width: 100%;
  height: 100%;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 0.5s;
}

.card-inner {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
}

.card-inner.flipped {
  transform: rotateY(180deg);
}

.card-front,
.card-back {
  backface-visibility: hidden;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 12px;
  position: absolute;
}

.card-front {
  transform: rotateY(0deg);
  z-index: 2;
}

.card-back {
  transform: rotateY(180deg);
  z-index: 1;
}
</style>
