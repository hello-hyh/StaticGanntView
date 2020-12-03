<template>
  <div class="gantt-main">
    <table class="gantt-table">
      <thead>
        <tr class="gantt-table__row">
          <td class="gantt-table__row-fixed"></td>
          <div class="gantt-table__list gantt-table__list-head">
            <div
              v-for="(date, dateIndex) in dateArr"
              :key="dateIndex"
              class="gantt-table__item"
              :class="[currentMonthIndex === dateIndex ? 'gantt-table__item-current': '']"
            >{{ date }}</div>
          </div>
        </tr>
      </thead>
      <div class="gantt-table-tbody">
        <div
          class="gant-table-hr"
          :style="{ left: `${currentDayPosition}px` }"
        ></div>
        <tr
          class="gantt-table__row"
          v-for="(dataRow, dataIndex) in tableData"
          :key="dataIndex"
        >
          <td class="gantt-table__row-fixed">
            <div class="gantt-table__row-content">
              <span
                class="gantt-table__circle gantt-table__circle-head"
                :style="{ backgroundColor: dataRow.color }"
              ></span>
              <span class="gantt-table__row__text">{{ dataRow.type }}</span>
            </div>
          </td>
          <div
            class="gantt-table__list gantt-table__list-content"
            ref="listRef"
          >
            <div
              v-for="(items, itemIndex) in dataRow.formatItemData"
              :key="itemIndex"
              class="gantt-table__item"
            >
              <div
                class="gantt-table__rectangle"
                v-for="(col, colKey, colIndex) in items"
                :key="colIndex"
                :style="{ left: `${buildPosition(col.startDayNum)}px`, width: `${buildPosition(col.endDayNum)}px`, backgroundColor: col.color }"
                :title="`${colKey}-${col.text}`"
              >
                <span
                  class="gantt-table__circle gantt-table__circle-col"
                  :style="{ backgroundColor: dataRow.color }"
                ></span> <span class="gantt-table-col_text">{{ `${colKey}-${col.text}` }}</span>
              </div>
            </div>
          </div>
        </tr>
      </div>
    </table>
  </div>
</template>
<script>
import moment from 'moment'
export default {
  props: {
    dateSection: {
      type: Object,
      default: () => {
        return {
          start: '2020-10-01',
          end: '2021-9-30',
          type: 'month'
        }
      }
    }
  },
  data () {
    return {
      tableData: [{
        type: 'Enterprise',
        color: '#15cc7f',
        data: [
          { dataType: 'eQms', text: 'R1 Site', color: '#b8efd8', start: '2020-10-20', end: '2020-11-20' },
          { dataType: 'eQms', text: 'R2 testtset', color: '#b8efd8', start: '2020-11-20', end: '2020-11-22' },
          { dataType: 'eQms', text: 'R3 Site by Release', color: '#b8efd8', start: '2020-11-23', end: '2020-12-01' },
          { dataType: 'eLN', text: 'Impormantest', color: '#b8efd8', start: '2020-10-10', end: '2020-12-10' },
          { dataType: 'eLN', text: 'testtatatatataaaa', color: '#b8efd8', start: '2020-12-30', end: '2021-05-20' }
        ]
      }, {
        type: 'Customer',
        color: '#8675ff',
        data: [
          { dataType: 'eQms2', text: 'R1 Site', color: '#dad5ff', start: '2020-10-20', end: '2020-11-20' },
          { dataType: 'eQms2', text: 'R2 testtset', color: '#dad5ff', start: '2020-11-23', end: '2020-12-22' },
          { dataType: 'eQms2', text: 'R3 Site by Release', color: '#dad5ff', start: '2021-01-09', end: '2021-04-01' },
          { dataType: 'eLN3', text: 'Impormantest', color: '#dad5ff', start: '2021-04-10', end: '2021-05-10' },
          { dataType: 'eLN3', text: 'testtatatatataaaa', color: '#dad5ff', start: '2021-07-30', end: '2021-09-20' }
        ]
      }],
      colWidth: 0,
      currentDayPosition: 0
    }
  },
  created () {
    window.addEventListener('resize', () => {
      this.buildColWidth()
      this.buildCurrentDayPosition()
    })
  },
  mounted () {
    this.buildColWidth()
    this.buildCurrentDayPosition()
    this.tableData.forEach(t => {
      this.$set(t, 'formatItemData', this.formatItemData(t))
    })
    console.log('this', this.currentDayPosition, this.currentMonthIndex)
  },
  computed: {
    dateArr () {
      const arr = []
      for (let i = 0; i < 12; i++) {
        arr[i] = moment(this.dateSection.start).add(i, 'month').format('MMM.YYYY')
      }
      return arr
    },

    currentMonthIndex () {
      return this.dateArr.findIndex(t => t === moment().format('MMM.YYYY'))
    },
    diffDay () {
      return moment(this.dateSection.end).diff(moment(this.dateSection.start), 'days')
    }
  },
  methods: {
    formatItemData (item) {
      const obj = {}
      item.data.forEach(t => {
        this.formatColData(t)
        if (obj[t.dataType] === undefined) {
          obj[t.dataType] = [t]
        } else {
          obj[t.dataType].push(t)
        }
      })
      return obj
    },
    formatColData (col) {
      if (col.start && col.end) {
        col.startDayNum = moment(col.start).diff(moment(this.dateSection.start), 'days')
        col.endDayNum = moment(col.end).diff(moment(col.start), 'days')
      }
    },
    buildPosition (data, isGlobal = false) {
      return (isGlobal ? this.hrColWidth : this.colWidth) * data
    },
    buildColWidth () {
      const listDom = this.$refs.listRef[0]
      let r = 0
      if (listDom && listDom.clientWidth) {
        r = parseFloat((listDom.clientWidth / this.diffDay))
        console.log(listDom.clientWidth / this.diffDay, r)
      }
      this.colWidth = r
    },
    buildCurrentDayPosition () {
      const fiexdDom = document.querySelector('.gantt-table__row-fixed')
      console.log(fiexdDom.clientWidth)
      if (fiexdDom) {
        this.currentDayPosition = fiexdDom.clientWidth + (this.colWidth * moment().diff(moment(this.dateSection.start), 'days'))
      }
    }
  }
}
</script>
<style lang="scss">
@import "./app.scss";
</style>
