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
              <Bouquet></Bouquet>
            </div>
            <div
              class="card-back"
              :style="{ backgroundColor: colors[index] }"
            ></div>
          </div>
        </div>
      </div>
    </template>
  </div>
  <a-modal
    ref="modalRef"
    v-model:open="isModalShow"
    centered
    :maskClosable="false"
    :closable="false"
    :bodyStyle="{
      height: '300px',
    }"
  >
    <template #title>
      <div class="text-center">{{ texts[currentIndex] }} 💐</div>
    </template>

    <div
      class="h-300px"
      :style="{
        backgroundColor: colors[currentIndex],
      }"
    >
      {{ texts[currentIndex] }}
    </div>

    <template #footer>
      <div class="flex justify-center">
        <a-button @click="onAgain">再来一次</a-button>
      </div>
    </template>
  </a-modal>
</template>

<script setup>
import { ref, onMounted, onUnmounted, nextTick } from "vue";

import { cloneDeep, random } from "lodash-es";

import { gsap } from "gsap";

import luckyColors from "../js/colors";

import luckyTexts from "../js/lucky-texts";
import { promiseTimeout } from "@vueuse/core";

import Bouquet from "./bouquet.vue";

// 预定义的颜色数组
const colors = ref(cloneDeep(luckyColors));

// 预定义的幸运文本数组
const texts = ref(cloneDeep(luckyTexts));

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

let isFlipping = false;
let currentIndex = -1;
const flippedCards = ref(Array(64).fill(false));

const modalRef = ref(null);

const isModalShow = ref(false);

const flipCard = async (index) => {
  if (!isFlipping) {
    isFlipping = true;
    currentIndex = index;

    const cardInner =
      gridContainerRef.value.querySelectorAll(".card-inner")[index];
    gsap.to(cardInner, {
      rotateY: "+=180",
      duration: 0.5,
      onComplete: async () => {
        flippedCards.value[index] = !flippedCards.value[index];
        gsap.set(cardInner, { rotateY: "0" });
        isFlipping = false;

        await promiseTimeout(50);

        isModalShow.value = true;
      },
    });
  }
};

const shuffleArray = (arr) => {
  const n = arr.length;
  for (let i = n - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }
};

const initLuckyTextsAndColors = () => {
  shuffleArray(colors.value);
  shuffleArray(texts.value);
};

const onAgain = () => {
  isModalShow.value = false;
  initLuckyTextsAndColors();
};

onMounted(() => {
  updateGrid();
  initLuckyTextsAndColors();
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
