<template>
  <div class="window-container">

  </div>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "App",
});
</script>

<style>
.window-container {
  width: 100%;
  height: 100%;

  position: relative;
  overflow: hidden;

  -webkit-app-region: drag;

  background: linear-gradient(135deg, #0064FB 0%, #0074E0 100%);
  background-size: cover;

  &::after {
    content: '';

    width: 100%;
    height: 100%;

    position: absolute;
    top: 0;
    left: 0;

    background-image: url(/public/icons/texture.svg);
    opacity: 0.3;
  }
}
</style>
