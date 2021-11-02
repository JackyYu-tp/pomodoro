<template lang="pug">
.container
  //- ul.nav-wrapper
  //-   li.nav-item(v-for="item in navItem")
  //-     Icon.icon(:name="item", @click="setNowItem(item)")
  .content-wrapper
    .panel
      Mission(
        :waitingMissions="waitingMissions",
        :finishedMissions="finishedMissions",
        :nowMission="nowMission",
        :isCounting="isCounting",
        @setNowMission="handleSetNowMission",
        @add="handleModal(true)"
      )
    .timer-panel(
      :class="{ 'work-theme': totalTime === 1500, 'rest-theme': totalTime === 300 }"
    )
      .timer
        .border-wrapper
          .time {{ nowRemainTime }}
          svg.svg(height="432", width="432")
            circle(
              cx="216",
              cy="216",
              r="196",
              :stroke="themeColor",
              stroke-width="40",
              fill="currentColor",
              :style="{ strokeDasharray: `${circleLength} 1230.88` }"
            )
      .controll-panel
        Icon.icon(name="close", @click="handleCancelEvent")
        Icon.icon(v-if="!isCounting", name="play", @click="handleStartTimer")
        Icon.icon(v-else, name="pause", @click="handleStopTimer")
        Icon.icon(name="double_arrow", @click="handleChangeToRest")
  Modal(v-if="isShowModal")
    .add-wrapper
      .title 新增任務
      .content
        .mission-name 任務名稱
        input.name-input(v-model="addMission", type="text")
        .btn-wrapper
          .button.cancel(@click="handleCancel") 取消
          .button.confirm(@click="handleAddEvent") 確認
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
    Mission: () => import("@/components/Mission"),
    Modal: () => import("@/components/Modal")
  },
  data() {
    return {
      navItem: ["list", "music", "chart"],
      nowItem: "list",
      totalTime: 1500,
      remainTime: 10,
      timer: null,
      isCounting: false,
      nowMission: 0,
      waitingMissions: [
        {
          id: 1,
          isDoing: false,
          label: "行銷提案簡報完成"
        },
        {
          id: 2,
          isDoing: false,
          label: "線上雜誌銷售管道蒐集"
        },
        {
          id: 3,
          isDoing: false,
          label: "律師事務所課程行銷文案"
        },
        {
          id: 4,
          isDoing: false,
          label: "音樂會今日文案圖片設計"
        }
      ],
      finishedMissions: [
        {
          id: 5,
          isDoing: false,
          label: "行銷提案簡報完成"
        },
        {
          id: 6,
          isDoing: false,
          label: "線上雜誌銷售管道蒐集"
        },
        {
          id: 7,
          isDoing: false,
          label: "律師事務所課程行銷文案"
        },
        {
          id: 8,
          isDoing: false,
          label: "音樂會今日文案圖片設計"
        }
      ],
      isShowModal: false,
      addMission: ""
    }
  },
  computed: {
    circleLength() {
      return (1 - this.remainTime / this.totalTime) * 1230.88
    },
    themeColor() {
      return this.totalTime === 300 ? "#7E57C2" : "#80CBC4"
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
    },
    biggestId() {
      return Math.max(
        ...this.waitingMissions.concat(this.finishedMissions).map((event) => {
          return event.id
        })
      )
    }
  },
  methods: {
    // setNowItem(name) {
    //   this.nowItem = name
    // },
    handleSetNowMission(index) {
      if (index === this.nowMission) return
      this.nowMission = index
      this.handleStopTimer()
      this.totalTime = 1500
      this.remainTime = 1500
    },
    handleStartTimer() {
      this.isCounting = true
      this.timer = setInterval(() => {
        if (this.remainTime !== 0) {
          this.remainTime--
        } else {
          this.handleStopTimer()
          if (this.totalTime === 300) {
            this.handleEventFinish()
          }
          this.handleSetRestTime()
        }
      }, 1000)
    },
    handleChangeToRest() {
      if (this.totalTime === 300) return
      this.handleStopTimer()
      this.handleSetRestTime()
    },
    handleCancelEvent() {
      this.handleStopTimer()
      this.handleEventFinish()
      this.totalTime = 1500
      this.remainTime = 1500
    },
    handleStopTimer() {
      this.isCounting = false
      clearInterval(this.timer)
      this.timer = null
    },
    handleEventFinish() {
      let item = this.waitingMissions.splice(this.nowMission, 1)
      this.finishedMissions = [...this.finishedMissions, ...item]
    },
    handleSetRestTime() {
      if (this.totalTime === 1500) {
        this.totalTime = 300
        this.remainTime = 300
      } else {
        this.totalTime = 1500
        this.remainTime = 1500
      }
    },
    handleModal(status) {
      this.isShowModal = status
    },
    handleCancel() {
      this.handleModal(false)
      this.addMission = ""
    },
    handleAddEvent() {
      this.waitingMissions = [
        ...this.waitingMissions,
        {
          id: this.biggestId + 1,
          isDoing: false,
          label: this.addMission
        }
      ]
      this.handleCancel()
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
      transition: .3s
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
          border-radius: 50%
          .time
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
        max-width: 60%
        margin-top: 40px
        height: 78px
        display: flex
        justify-content: space-around
        align-items: center
        color: #fff
        border-radius: 20px
        .icon
          width: 15%
          cursor: pointer
          transition: .3s
          &:hover
            transform: scale(1.1)
    .work-theme
      .timer
        .border-wrapper
          border: 3px solid $primary
          .time
            color: $green-001
      .controll-panel
        background-color: $primary
    .rest-theme
      .timer
        .border-wrapper
          border: 3px solid $purple-001
          .time
            color: $purple-001
      .controll-panel
        background-color: $purple-002
  .add-wrapper
    width: 30vw
    border-radius: 20px
    overflow: hidden
    text-align: left
    background-color: #fff
    .title
      padding: 8px 27px
      font-size: 1.5rem
      color: $grey-003
      letter-spacing: 2.4px
      line-height: 36px
      background-color: $cyan-001
    .content
      padding: 20px 30px
    .mission-name
      color: $grey-006
      font-size: 1rem
      letter-spacing: 1.6px
    .name-input
      width: 100%
      margin-top: 6px
      border: none
      border-bottom: 2px solid $grey-001
      font-size: 18px
      color: $grey-003
      transition: .3s
      &:focus-visible
        outline: none
    .btn-wrapper
      margin-top: 30px
      display: flex
      justify-content: flex-end
      align-items: center
      .button
        padding: 8px 36px
        border-radius: 20px
        color: $grey-003
        display: flex
        align-items: center
        justify-content: center
        cursor: pointer
        &.cancel
          background-color: $grey-007
          margin-right: 10px
        &.confirm
          background-color: $cyan-001
</style>
