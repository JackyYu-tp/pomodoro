<template lang="pug">
.mission-wrapper
  .waiting-mission
    .header-wrapper
      p.title 待辦任務
      .function-wrapper
        icon.icon(v-for="item in functionItems", :name="item", :key="item")
    ul.list
      li.list-item(v-for="(item, index) in waitingItems", :key="item.id")
        .status(:class="item.finished ? 'finished' : 'waiting'")
        p.label {{ item.label }}
        .item-functions
          icon.icon.remove-icon(name="remove_outline")
          icon.icon(
            :name="item.isDoing ? 'pause' : 'play'",
            :class="{ 'now-item': index === 0 }"
          )
  .finished-mission
    .header-wrapper
      p.title 已完成任務
    ul.list
      li.list-item(v-for="(item, index) in waitingItems", :key="item.id")
        icon.icon(name="check")
        p.label {{ item.label }}
</template>

<script>
import "@/assets/svg/sort.svg"
import "@/assets/svg/add.svg"
import "@/assets/svg/remove_outline.svg"
import "@/assets/svg/check.svg"
import "@/assets/svg/play.svg"
import "@/assets/svg/pause.svg"
import Icon from "@/components/Icon.vue"

export default {
  name: "Mission",
  components: {
    Icon
  },
  data() {
    return {
      functionItems: ["sort", "add"],
      nowMission: null,
      missions: [
        {
          id: 1,
          finished: false,
          isDoing: false,
          label: "行銷提案簡報完成"
        },
        {
          id: 2,
          finished: false,
          isDoing: false,
          label: "線上雜誌銷售管道蒐集"
        },
        {
          id: 3,
          finished: false,
          isDoing: false,
          label: "律師事務所課程行銷文案"
        },
        {
          id: 4,
          finished: false,
          isDoing: false,
          label: "音樂會今日文案圖片設計"
        }
      ]
    }
  },
  computed: {
    waitingItems() {
      return this.missions.filter((item) => !item.false)
    }
  },
  methods: {}
}
</script>
<style lang="sass">
@import "@/assets/sass/index.sass"
.function-wrapper
  .icon
    width: 30px
    height: 30px
    cursor: pointer
.item-functions
  .icon
    width: 18px
    height: 18px
    color: $grey-001
    &:not(:last-child)
      margin-right: 16px
  .remove-icon
    cursor: pointer
  .now-item
    color: #FF8F00
.finished-mission
  .icon
    margin-right: 6px
    width: 20px
    height: 20px
</style>

<style lang="sass" scoped>
@import "@/assets/sass/index.sass"
.mission-wrapper
  margin-left: 69px
  width: 430px
  border-radius: 20px
  overflow: hidden
  box-shadow: 0px 3px 6px #00000029
  .waiting-mission
    .header-wrapper
      padding: 8px 20px 8px 27px
      background-color: $cyan-001
      display: flex
      justify-content: space-between
      align-items: center
    .list
      padding: 14px 27px
      height: 300px
      overflow-y: scroll
      .list-item
        padding: 13px 6px
        display: flex
        align-items: center
        border-bottom: 2px solid $grey-001
        transition: .3s
        &:hover
          background-color: $grey-005
        .status
          margin-right: 6px
          &.waiting
            width: 14px
            height: 14px
            border: 3px solid $cyan-002
            border-radius: 50%
        .label
          flex: 1
          text-align: left
  .finished-mission
    .header-wrapper
      padding: 8px 27px
      background-color: $grey-002
      display: flex
      align-items: center
    .list
      padding: 14px 27px
      height: 170px
      overflow-y: scroll
      .list-item
        padding: 13px 6px
        display: flex
        align-items: center
        border-bottom: 2px solid $grey-001
        transition: .3s
        &:hover
          background-color: $grey-005
  .title
    color: $grey-003
    font-size: 24px
    font-weight: 500
    letter-spacing: 2.4px
</style>
