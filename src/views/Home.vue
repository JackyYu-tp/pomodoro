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
        @add="handleAddModal(true)",
        @finish="handleSingleEventFinish"
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
        Icon.icon(
          name="close",
          @click="handleCancelEventModal(true)",
          :class="{ disabled: waitingMissions.length <= 0 || !isCounting }"
        )
        Icon.icon(
          v-if="!isCounting",
          name="play",
          @click="handleStartTimer",
          :class="{ disabled: waitingMissions.length <= 0 }"
        )
        Icon.icon(v-else, name="pause", @click="handleStopTimer")
        Icon.icon(
          name="double_arrow",
          @click="handleChangeToRestModal(true)",
          :class="{ disabled: waitingMissions.length <= 0 || totalTime === 300 }"
        )
  Modal(v-if="isShowAddModal")
    .modal-wrapper.add-wrapper
      .title 新增任務
      .content
        .mission-name 任務名稱
        input.name-input(v-model="addMission", type="text")
        .btn-wrapper
          .button.cancel(@click="handleCancelAddEvent(false)") 取消
          .button.confirm(@click="handleAddEvent") 確認
  Modal(v-if="isShowChangeToRestModal")
    .modal-wrapper.confirm-wrapper
      .title 跳至休息時間
      .content
        .message 您確定要終止目前任務？
        .btn-wrapper
          .button.cancel(@click="handleChangeToRestModal(false)") 取消
          .button.confirm(@click="handleChangeToRest") 確認
  Modal(v-if="isShowCancelEventModal")
    .modal-wrapper.confirm-wrapper
      .title 終止任務
      .content
        .message 您確定要終止目前任務？
        .btn-wrapper
          .button.cancel(@click="handleCancelEventModal(false)") 取消
          .button.confirm(@click="handleCancelEvent") 確認
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
      remainTime: 1500,
      timer: null,
      isCounting: false,
      nowMission: 0,
      waitingMissions: [],
      finishedMissions: [],
      isShowAddModal: false,
      isShowChangeToRestModal: false,
      isShowCancelEventModal: false,
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
      let allMissions = this.waitingMissions.concat(this.finishedMissions)
      if (allMissions.length <= 0) return 0
      return Math.max(
        ...allMissions.map((event) => {
          return event.id
        })
      )
    }
  },
  mounted() {
    this.handleGetList()
  },
  beforeDestroy() {
    this.handleSaveList()
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
      if (this.waitingMissions.length <= 0) return
      this.isCounting = true
      this.timer = setInterval(() => {
        if (this.remainTime !== 0) {
          this.remainTime--
        } else {
          this.handleStopTimer()
          if (this.totalTime === 300) {
            this.handleEventFinish(this.nowMission)
          }
          this.handleSetRestTime()
        }
      }, 1000)
    },
    handleChangeToRest() {
      if (this.totalTime === 300) return
      this.handleStopTimer()
      this.handleSetRestTime()
      this.handleChangeToRestModal(false)
    },
    handleCancelEvent() {
      this.handleStopTimer()
      this.handleEventFinish(this.nowMission)
      this.handleCancelEventModal(false)
      this.totalTime = 1500
      this.remainTime = 1500
    },
    handleSingleEventFinish(index) {
      if (index === this.nowMission) {
        this.handleStopTimer()
        this.totalTime = 1500
        this.remainTime = 1500
      }
      this.handleEventFinish(index)
    },
    handleStopTimer() {
      if (!this.timer) return
      this.isCounting = false
      clearInterval(this.timer)
      this.timer = null
    },
    handleEventFinish(index) {
      let item = this.waitingMissions.splice(index, 1)
      this.finishedMissions = [...this.finishedMissions, ...item]
      this.nowMission = 0
      this.handleSaveList()
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
    handleAddModal(status) {
      this.isShowAddModal = status
    },
    handleCancelAddEvent() {
      this.handleAddModal(false)
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
      this.handleCancelAddEvent()
      this.handleSaveList()
    },
    handleSaveList() {
      localStorage.setItem(
        "waitingMissions",
        JSON.stringify(this.waitingMissions)
      )
      localStorage.setItem(
        "finishedMissions",
        JSON.stringify(this.finishedMissions)
      )
    },
    handleGetList() {
      let waitingMissions = localStorage.getItem("waitingMissions")
      if (waitingMissions) {
        waitingMissions = JSON.parse(waitingMissions)
      }
      if (waitingMissions && waitingMissions.length > 0) {
        this.waitingMissions = waitingMissions
      }

      let finishedMissions = localStorage.getItem("finishedMissions")
      if (finishedMissions) {
        finishedMissions = JSON.parse(finishedMissions)
      }
      if (finishedMissions && finishedMissions.length > 0) {
        this.finishedMissions = finishedMissions
      }
    },
    handleChangeToRestModal(status) {
      this.isShowChangeToRestModal = status
    },
    handleCancelEventModal(status) {
      this.isShowCancelEventModal = status
    }
  }
}
</script>
<style lang="sass">
.disabled
  pointer-events: none
</style>

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
    padding: 20px
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
  .modal-wrapper
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
  .add-wrapper
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
  .confirm-wrapper
    .content
      .message
        font-size: 18px
        color: $grey-003
        letter-spacing: 1.8px
        line-height: 27px
</style>
