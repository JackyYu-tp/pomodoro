<template lang="pug">
.container
  ul.nav-wrapper
    li.nav-item(v-for="item in navItem")
      Icon.icon(:name="item", @click="setNowItem(item)")
  .content-wrapper
    .panel
      Mission
</template>

<script>
import "@/assets/svg/list.svg"
import "@/assets/svg/music.svg"
import "@/assets/svg/chart.svg"
import Icon from "@/components/Icon.vue"

export default {
  name: "Home",
  components: {
    Icon,
    Mission: () => import("@/components/Mission")
  },
  data() {
    return {
      navItem: ["list", "music", "chart"],
      nowItem: "list"
    }
  },
  methods: {
    setNowItem(name) {
      this.nowItem = name
    }
  }
}
</script>

<style lang="sass" scoped>
@import "@/assets/sass/index.sass"
.container
  display: flex
  .nav-wrapper
    padding-top: 64px
    width: 7vw
    height: 100vh
    background-color: $primary
    .nav-item
      width: 7vw
      height: 7vw
      cursor: pointer
      display: flex
      justify-content: center
      align-items: center
      transition: .3s
      &:hover
        background-color: $cyan-003
      .icon
        width: 60%
  .content-wrapper
    flex: 1
    background-color: $grey-004
    display: flex
    .panel
      margin-top: 64px
</style>
