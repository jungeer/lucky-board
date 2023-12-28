<template>
  <div class="h-100% w-100% homeview">
    <lucky-board></lucky-board>
  </div>
</template>

<script setup lang="ts">
import { h } from "vue";

import { Modal } from "ant-design-vue";

import LuckyBoard from "./lucky-board/index.vue";

import FlowerIcon from "./flower-icon.vue";

const content = [
  h("p"),
  h("p", "幸运翻翻乐 💐 ~ "),
  h("p", "一定能带给你幸运的幸运幸运幸运板 🎉🎉 ~~ "),
  h("p", "规则就是没有任何理由的幸运值爆满 🚀🚀🚀 ~~~ "),
];

Modal.info({
  title: "",
  icon: h(FlowerIcon),
  content: h("div", content),
  okText: "确定",
  closable: false,
  centered: true,
  maskClosable: false,
});
</script>
