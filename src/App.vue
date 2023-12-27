<script setup>
import { RouterView } from "vue-router";
</script>

<template>
  <RouterView />
</template>

<style>
html,
body,
#app {
  height: 100%;
  width: 100%;
  padding: 0;
  overflow: hidden;
}

#app {
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
