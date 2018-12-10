<template>
  <div class="__vev_calendar-wrapper">
    <cal-panel
      :events="events"
      :calendar="calendarOptions"
      :selectedDay='selectedDayEvents.date'
      @cur-day-changed="handleChangeCurDay"
      @month-changed="handleMonthChanged">
    </cal-panel>
    <cal-events
      :title="title"
      :dayEvents="selectedDayEvents"
      :locale="calendarOptions.options.locale"
      :color="calendarOptions.options.color">
      <slot :showEvents="selectedDayEvents.events"></slot>
    </cal-events>
  </div>
</template>
<script>
import { isEqualDateStr} from './tools.js'

import calEvents from './components/cal-events.vue'
import calPanel from './components/cal-panel.vue'

const inBrowser = typeof window !== 'undefined'
export default {
  name: 'vue-event-calendar',
  components: {
    'cal-events': calEvents,
    'cal-panel': calPanel
  },
  data () {
    return {
      selectedDayEvents: {
        date: 'all',
        events: this.events || []  //default show all event
      }
    }
  },
  props: {
    title: String,
    events: {
      type: Array,
      required: true,
      default: [],
      validator (events) {
        let validate = true
        events.forEach((event, index) => {
          if (!event.date) {
            console.error('Vue-Event-Calendar-Error:' + 'Prop events Wrong at index ' + index)
            validate = false
          }
        })
        return validate
      }
    }
  },
  computed: {
    calendarOptions () {
      let dateObj = new Date()
      if (inBrowser) {
          return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA
      } else {
        return {
          options: {
            locale: 'zh', //zh
            color: ' rgb(254,91,48)'
          },
          params: {
              curYear: dateObj.getFullYear(),
              curMonth: dateObj.getMonth(),
              curDate: dateObj.getDate(),
              curEventsDate: 'all'
          }
        }
      }
    },
    calendarParams () {
      let dateObj = new Date()
      if (inBrowser) {
          return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA.params
      } else {
        return {
          curYear: dateObj.getFullYear(),
          curMonth: dateObj.getMonth(),
          curDate: dateObj.getDate(),
          curEventsDate: 'all'
        }
      }
    }
  },
  created () {
    if (this.calendarParams.curEventsDate !== 'all') {
      this.handleChangeCurDay(this.calendarParams.curEventsDate)
    }
  },
  methods: {
    handleChangeCurDay (date) {
      let events = this.events.filter(function(event) {
        return isEqualDateStr(event.date, date)
      })
      this.selectedDayEvents = {
        date: date,
        events: events
      }
      this.$emit('day-changed', {
        date: date,
        events: events
      })
    },
    handleMonthChanged (yearMonth) {
      this.$emit('month-changed', yearMonth)
    }
  },
  watch: {
    calendarParams () {
      if (this.calendarParams.curEventsDate !== 'all') {
        let events = this.events.filter(event => {
          return isEqualDateStr(event.date, this.calendarParams.curEventsDate)
        })
        this.selectedDayEvents = {
          date: this.calendarParams.curEventsDate,
          events
        }
      } else {
        this.selectedDayEvents = {
          date: 'all',
          events: this.events
        }
      }
    },
    events () {
      this.selectedDayEvents = {
        date: 'all',
        events: this.events || []
      }
    }
  }
}
</script>
<style lang="less">
@base-orange: rgb(254,91,48);
@white: #ffffff;
@gray: #e0e0e0;
@gray-dark: #b1b1b1;
@large-padding: 15px;
@small-padding: 10px;

@icon-border-size: 1px;
@media screen and (min-width: 768px) {
  .__vev_calendar-wrapper{
    max-width: 1200px;
    margin: 0 auto;
    padding-top: 50px;
    .cal-wrapper{
      width: 100%;
      .cal-header{
        position: absolute;
        top: 0;
        left: 200px;
        width: 500px;
        text-align: center;
        .l,.r{
          cursor: pointer;
          position: absolute;
          top: 0;
        }
        .l{
          left: 0;
        }
        .r{
          right: 0
        }
      }
      .date-num{
        line-height: 65px;
        color: #333;
      }
    }
    .today{
      .date-num{
        color: rgb(41,138,255);
      }
    }
    .event{
      .date-num{
        color: rgb(254,91,48);
      }
    }
    .events-wrapper{
      margin-top:  50px;
      width: 40%;
      color: @white;
      padding: 20px 25px 40px;
      position: absolute;
      left: 60%;
      top: 0;
      bottom: 0;
    }
  }
}
@media screen and (max-width: 767px) {
  .__vev_calendar-wrapper{
    .cal-wrapper{
      width: 100%;
      padding: 10px 5px;
      .date-num{
        line-height: 60px;
      }
    }
    .events-wrapper{
      width: 100%;
      margin-top: 10px;
      padding: 10px;
    }
  }
}
.__vev_calendar-wrapper{
  position: relative;
  overflow: hidden;
  width: 100%;
  *{
    box-sizing: border-box;
  }
  ::-webkit-scrollbar{
    width: 8px;
    height: 8px;
  }
  ::-webkit-scrollbar-track {
    box-shadow: inset 0 0 2px rgba(0,0,0,.2);
    border-radius: 5px;
  }
  ::-webkit-scrollbar-thumb {
    border-radius: 5px;
    background: rgba(0,0,0,.2);
  }
  .cal-wrapper{
    .cal-body{
      width: 60%;
      float: left;
      border: 1px solid #eee;
      padding: 10px;
      .weeks{
        width: 100%;
        overflow: hidden;
        text-align: center;
        font-size: 1rem;
        .item{
          line-height: 50px;
          float: left;
          width: 14.285%;
        }
      }
      .dates{
        width: 100%;
        overflow: hidden;
        text-align: center;
        font-size: 1rem;
        .item{
          position: relative;
          float: left;
          display: block;
          width: 14.285%;
          cursor: default;
          -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
          .date-num{
            font-size: 1rem;
            position: relative;
            z-index: 3;
            cursor: pointer
          }
          &.selected-day{
            p{
              color: #fff
            }
            .eventSpan{
              background: rgb(41,138,255);
            }
            .is-event{
              background-color: rgb(254,91,48);
            }
          }
          .eventSpan{
            content: '';
            border-radius: 50%;
            width: 36px;
            height: 36px;
            position: absolute;
            left: 50%;
            top: 50%;
            z-index: 1;
            margin-left: -17px;
            margin-top: -17px;
          }
          .is-event-dot{
            content: '';
            background: rgb(254,91,48);
            border-radius: 50%;
            opacity: .8;
            width: 12px;
            height: 4px;
            position: absolute;
            left: 50%;
            top: 80%;
            z-index: 2;
            margin-left: -6px;
            margin-top: 8px;
          }
        }
      }
    }
  }
  .events-wrapper{
    margin-left: -1px;
    border: 1px solid #eee;
    .cal-events{
      height: 100%;
      overflow-y: auto;
      padding: 10px 5px 0;
      margin: 15px 0;
    }
    .date{
      color: #333;
      font-size: 20px;
      font-weight: 100;
    }
    .event-item{
      padding: 5px 20px;
      margin-top: 15px;
      box-shadow: 0 3px 11px 2px rgba(41,138,255, .1);
      background-color: #fff;
      border-radius: 5px;
      color: #323232;
      position: relative;
      &:first-child{
        margin-top: 0;
      }
      .title{
        height: 40px;
        line-height: 40px;
        color: #323232;
        font-size: 16px;
        border-bottom: 1px solid #f2f2f2;
      }
      .time{
        position: absolute;
        right: 30px;
        top: 17px;
        color: #9b9b9b;
        font-size: 14px;
      }
      .desc{
        color: #9b9b9b;
        font-size: 14px;
        padding: 7px 0;
      }
    }
  }
  .arrow-left.icon {
    color: #000;
    position: absolute;
    left: 6%;
    margin-top: 10px;
  }
  .arrow-left.icon:before {
    content: '';
    position: absolute;
    left: 1px;
    top: -5px;
    width: 10px;
    height: 10px;
    border-top: solid @icon-border-size currentColor;
    border-right: solid @icon-border-size currentColor;
    -webkit-transform: rotate(-135deg);
            transform: rotate(-135deg);
  }
  .arrow-right.icon {
    color: #000;
    position: absolute;
    right: 6%;
    margin-top: 10px;
  }
  .arrow-right.icon:before {
    content: '';
    position: absolute;
    right: 1px;
    top: -5px;
    width: 10px;
    height: 10px;
    border-top: solid @icon-border-size currentColor;
    border-right: solid @icon-border-size currentColor;
    -webkit-transform: rotate(45deg);
            transform: rotate(45deg);
  }
  h3, p{
    margin: 0;
    padding: 0;
  }
}

</style>
