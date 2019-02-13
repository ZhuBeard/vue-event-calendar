<template>
  <div class="events-wrapper" style="overflow: hidden">
    <h2 class="date" style="
    width: 70%;
    line-height: 26px;
    float: left;
    margin: 0;
    font-size: 16px;">
      {{dayEventsTitle}}
    </h2>
    <a :href="'/myaccount/returnBill?date='+dayEvents.date" style="
    float: right;
    display: inline-block;
    line-height: 26px;
    width: 30%;
    text-align: right;">回款账单 &nbsp;></a>
    <div class="cal-events">
      <slot>
        <div v-for="(event, index) in events" class="event-item" :key="index">
          </svg-icon><cal-event-item :event="event" :index="index" :locale="locale"></cal-event-item>
        </div>
      </slot>
    </div>
  </div>
</template>

<script>
import i18n from '../i18n.js'
import { dateTimeFormatter } from '../tools.js'
import calEventItem from './cal-event-item.vue'
export default {
  name: 'cal-events',
  data () {
    return {
      i18n,
      tabTitle: ''
    }
  },
  components: {
    'cal-event-item': calEventItem
  },
  props: {
    title: String,
    dayEvents: {
      type: Object,
      required: true
    },
    locale: {
      type: String,
      required: true
    },
    color: {
      type: String,
      required: true
    }
  },
  computed: {
    dayEventsTitle () {
      if (this.title) return this.title
      if (this.dayEvents.date !== 'all') {
        let tempDate
        if (this.dayEvents.events.length !== 0) {
          tempDate = Date.parse(new Date(this.dayEvents.events[0].date))
          return dateTimeFormatter(tempDate, i18n[this.locale].fullFormat)
        } else {
          tempDate = dateTimeFormatter(Date.parse(new Date(this.dayEvents.date)), i18n[this.locale].fullFormat)
          return `${tempDate} ${i18n[this.locale].notHaveEvents}`
        }
      } 
      else {
        return this.tabTitle
      }
    },
    events () {
      return this.dayEvents.events
    }
  },
  watch: {
    dayEvents(oldVal, newVal){
      let tempDate
      if(oldVal.date != 'all' && newVal.date == 'all'){
        if (this.dayEvents.events.length !== 0) {
          tempDate = Date.parse(new Date(oldVal.date))
          this.tabTitle = dateTimeFormatter(tempDate, i18n[this.locale].fullFormat)
        } else {
          tempDate = dateTimeFormatter(Date.parse(new Date(oldVal.date)), i18n[this.locale].fullFormat)
          this.tabTitle = `${tempDate} ${i18n[this.locale].notHaveEvents}`
        }
      }else if (newVal.date != 'all'){
        if (this.dayEvents.events.length !== 0) {
          tempDate = Date.parse(new Date(newVal.date))
          this.tabTitle = dateTimeFormatter(tempDate, i18n[this.locale].fullFormat)
        } else {
          tempDate = dateTimeFormatter(Date.parse(new Date(newVal.date)), i18n[this.locale].fullFormat)
          this.tabTitle = `${tempDate} ${i18n[this.locale].notHaveEvents}`
        }
      }else{

      }
    }
  },
  methods: {
    dateTimeFormatter
  }
}
</script>