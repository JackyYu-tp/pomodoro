<template lang="pug">
.modal
  slot
</template>

<script>
export default {
  name: "Modal",
  props: {},
  data() {
    return {}
  },
  methods: {}
}
</script>

<style lang="sass" scoped>
.modal
  position: absolute
  top: 0
  left: 0
  width: 100vw
  height: 100vh
  background-color: rgba(0, 0, 0, .4)
  display: flex
  align-items: center
  justify-content: center
</style>
