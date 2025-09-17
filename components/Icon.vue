<template>
  <div
    class="icon"
    :style="{
      width: width,
      height: height,
      backgroundImage: `url(${url})`
    }"
  >
  </div>
</template>

<script>
export default {
  name: 'Icon',
  props: {
    url: {
      type: String,
      required: true
    },
    width: {
      type: String,
      default: '1rem'
    },
    height: {
      type: String,
      default: '1rem'
    }
  }
}
</script>

<style scoped>
.icon {
  display: inline-block;
  background-position: center;
  background-size: contain;
  background-repeat: no-repeat;
  z-index: 2;
}
</style>
