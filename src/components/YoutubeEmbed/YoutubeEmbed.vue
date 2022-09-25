<template>
  <div class="youtube-embed">
    <slot></slot>
  </div>
</template>

<style scoped lang="sass">
.youtube-embed
  float: none
  clear: both
  width: 100%
  position: relative
  padding-bottom: 56.25%
  padding-top: 25px
  height: 0
  iframe
    position: absolute
    top: 0
    left: 0
    width: 100%
    height: 100%
</style>
