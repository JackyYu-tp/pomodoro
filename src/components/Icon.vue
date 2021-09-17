<template lang="pug">
svg.icon
  use(:xlink:href="`#${name}`")
</template>

<script>
export default {
  name: "Icon",
  props: {
    name: String
  },
  data() {
    return {}
  }
}
</script>

<style lang="sass">
.icon
    color: white
svg
    fill: white
    width: 100%
    height: 100%
</style>
