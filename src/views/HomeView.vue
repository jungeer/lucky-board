<template>
  <div class="h-100% w-100% homeview">
    <lucky-board></lucky-board>
  </div>
</template>

<script setup lang="ts">
  import { ref } from "vue";

  import { Modal } from "ant-design-vue";

  import LuckyBoard from "./lucky-board/index.vue";

  Modal.info({
    title: "欢迎使用",
    content: "请开始您的翻牌之旅 ~ 规则就是没有规则",
    okText: "确定",
    closable: false,
    centered: true,
    maskClosable: false,
  })
</script>
