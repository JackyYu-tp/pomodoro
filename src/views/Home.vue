<template lang="pug">
.container
  //- ul.nav-wrapper
  //-   li.nav-item(v-for="item in navItem")
  //-     Icon.icon(:name="item", @click="setNowItem(item)")
  .content-wrapper
    .panel
      Mission
    .timer-panel
      .timer
        .border-wrapper
          .time {{ nowRemainTime }}
          svg.svg(height="432", width="432")
            circle(
              cx="216",
              cy="216",
              r="196",
              stroke="#80CBC4",
              stroke-width="40",
              fill="currentColor",
              :style="{ strokeDasharray: `${circleLength} 1230.88` }"
            )
      .controll-panel
        Icon.icon(name="close")
        Icon.icon(v-if="!isCounting", name="play", @click="startTimer")
        Icon.icon(v-else, name="pause", @click="stopTimer")
        Icon.icon(name="double_arrow")
</template>

<script>
// import "@/assets/svg/list.svg"
// import "@/assets/svg/music.svg"
// import "@/assets/svg/chart.svg"
import "@/assets/svg/play.svg"
import "@/assets/svg/pause.svg"
import "@/assets/svg/close.svg"
import "@/assets/svg/double_arrow.svg"
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
      nowItem: "list",
      totalTime: 1500,
      remainTime: 1400,
      timer: null,
      isCounting: false
    }
  },
  computed: {
    circleLength() {
      return (1 - this.remainTime / this.totalTime) * 1230.88
    },
    nowRemainTime() {
      return (
        Math.floor(this.remainTime / 60)
          .toString()
          .padStart(2, "0") +
        ":" +
        Math.floor(this.remainTime % 60)
          .toString()
          .padStart(2, "0")
      )
    }
  },
  methods: {
    setNowItem(name) {
      this.nowItem = name
    },
    startTimer() {
      this.isCounting = true
      this.timer = setInterval(() => {
        this.remainTime--
      }, 1000)
    },
    stopTimer() {
      this.isCounting = false
      clearInterval(this.timer)
      this.timer = null
    }
  }
}
</script>

<style lang="sass" scoped>
@import "@/assets/sass/index.sass"
.container
  width: 100vw
  height: 100vh
  display: flex
  background-color: $grey-004
  // .nav-wrapper
  //   padding-top: 64px
  //   width: 7vw
  //   height: 100vh
  //   background-color: $primary
  //   .nav-item
  //     width: 7vw
  //     height: 7vw
  //     cursor: pointer
  //     display: flex
  //     justify-content: center
  //     align-items: center
  //     transition: .3s
  //     &:hover
  //       background-color: $cyan-003
  //     .icon
  //       width: 60%
  .content-wrapper
    flex: 1
    display: flex
    gap: 10px
    max-width: 1024px
    margin: 0 auto
    justify-content: space-evenly
    align-items: center
    .panel
    .timer-panel
      display: flex
      flex-direction: column
      align-items: center
      .timer
        width: 432px
        height: 432px
        box-shadow: 0px 3px 16px #00000029
        border-radius: 50%
        position: relative
        display: flex
        justify-content: center
        align-items: center
        .border-wrapper
          width: 392px
          height: 392px
          border: 3px solid $primary
          border-radius: 50%
          .time
            color: $green-001
            font-size: 74px
            font-weight: bold
            line-height: 392px
          .svg
            position: absolute
            top: 0
            left: 0
            color: rgba(256, 256, 256, 0)
            opacity: 0.3
            transform: rotate(-90deg)
            transition: .3s
      .controll-panel
        max-width: 85%
        margin-top: 40px
        height: 78px
        display: flex
        background-color: $primary
        color: #fff
        border-radius: 20px
        .icon
          cursor: pointer
          transition: .3s
          &:hover
            transform: scale(1.1)
</style>
