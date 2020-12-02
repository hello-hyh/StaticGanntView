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
            >{{ date }}</div>
          </div>
        </tr>
      </thead>
      <tbody>
        <tr
          class="gantt-table__row"
          v-for="(dataRow, dataIndex) in tableData"
          :key="dataIndex"
        >
          <td class="gantt-table__row-fixed">
            <div class="gantt-table__row-content">
              <span
                class="gantt-table__row__circle"
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
              v-for="(item, itemIndex) in dataRow.data"
              :key="itemIndex"
              class="gantt-table__item"
            >
              {{ item }}
              <div class="gantt-table__rectangle">

              </div>
            </div>
          </div>
        </tr>
      </tbody>
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
          { dataType: 'eQms', text: 'R1 Site', color: '#15cc7f', start: '2020-10-20', end: '2020-11-20' },
          { dataType: 'eQms', text: 'R2 testtset', color: '#15cc7f', start: '2020-10-20', end: '2020-10-22' },
          { dataType: 'eQms', text: 'R3 Site by Release', color: '#15cc7f', start: '2020-10-09', end: '2020-11-01' },
          { dataType: 'eLN', text: 'Impormantest', color: '#15cc7f', start: '2020-10-10', end: '2020-12-10' },
          { dataType: 'eLN', text: 'testtatatatataaaa', color: '#15cc7f', start: '2020-10-30', end: '2021-05-20' }
        ]
      }, {
        type: 'Customer',
        color: '#8675ff',
        data: [
          { dataType: 'eQms2', text: 'R1 Site', color: '#8675ff', start: '2020-10-20', end: '2020-11-20' },
          { dataType: 'eQms2', text: 'R2 testtset', color: '#8675ff', start: '2020-10-20', end: '2020-10-22' },
          { dataType: 'eQms2', text: 'R3 Site by Release', color: '#8675ff', start: '2020-10-09', end: '2020-11-01' },
          { dataType: 'eLN3', text: 'Impormantest', color: '#8675ff', start: '2020-10-10', end: '2020-12-10' },
          { dataType: 'eLN3', text: 'testtatatatataaaa', color: '#8675ff', start: '2020-10-30', end: '2021-05-20' }
        ]
      }]
    }
  },
  mounted () {
    this.formatItemData(this.tableData[0].data)
  },
  computed: {
    dateArr () {
      const arr = []
      for (let i = 0; i < 12; i++) {
        arr[i] = moment(this.dateSection.start).add(i, 'month').format('MMM.YYYY')
      }
      return arr
    },
    colWidth () {
      const listDom = this.$refs.listRef[0]
      let r = 0
      if (listDom && listDom.clientWidth) {
        const diffDay = moment(this.dateSection.end).diff(moment(this.dateSection.start), 'days')
        console.log(diffDay)
        r = parseInt(listDom.clientWidth / diffDay)
      }
      console.log(r)
      return r
    }
  },
  methods: {
    formatItemData (data) {
      const obj = {}
      data.forEach(t => {
        this.formatColData(t)
        if (obj[t.dataType] === undefined) {
          obj[t.dataType] = [t]
        } else {
          obj[t.dataType].push(t)
        }
      })
      console.log(obj)
    },
    formatColData (col) {
      if (col.start && col.end) {
        col.startDayNum = moment(col.start).diff(moment(this.dateSection.start), 'days')
        col.endDayNum = moment(col.end).diff(moment(col.start), 'days')
      }
    }
  }
}
</script>
<style lang="scss">
@import "./app.scss";
</style>
