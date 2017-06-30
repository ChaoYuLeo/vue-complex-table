<template>
  <div class="complex-table-all position-relative clearfix">
    <table ref="leftTable" class="complex-entity-table position-absolute complex-left-table complex-table complex-table-striped complex-table-bordered">
      <colgroup>
        <col v-for="(column, index) in leftBaseColumns" :width="column.width" >
      </colgroup>
      <thead>
        <tr>
          <th v-for="column in leftBaseColumns" :rowspan="headRows" :colspan="column.colspans && column.colspans.length" :style="column.style">
            {{column.label}}
          </th>
        </tr>
        <tr v-if="headRows != 1">
        </tr>
      </thead>

      <tbody v-tr-hight="{this: self, index: index}" v-for="(tbody, index) in data" :class="{'tr-hover': additionalSets[index]._isHover}" @mouseenter="additionalSets[index]._isHover = true" @mouseleave="additionalSets[index]._isHover = false">
        <tr track-by="$index" :style="{height: additionalSets[index]._height + 'px'}">
          <td v-for="column in leftBaseColumns" :align="column.align || 'center'" :rowspan="(rowspanKey && tbody[rowspanKey].length) || 1">
            <cell :row="tbody" :column="column" :index="index"></cell>
          </td>
        </tr>
        <tr :style="{height: additionalSets[index]._height + 'px'}" track-by="$index" v-for="rows in tbody[rowspanKey] && tbody[rowspanKey].length" v-if="rows != 1">
        </tr>
      </tbody>
    </table>

    <div class="complex-center-table-scroll optiscroll" ref="centerTableScroll" :style="centerTableStyle">
      <table ref="centerTable" class="complex-entity-table complex-table complex-table-striped complex-table-bordered" style="width: 100%;">
        <colgroup>
          <col v-for="(column, index) in centerSecondColumns" :width="column.width" >
        </colgroup>
        <thead>
          <tr>
            <th v-for="column in centerFirstColumns" :rowspan="!column.colspans && headRows" :colspan="column.colspans && column.colspans.length" :style="column.style">
              {{column.label}}
            </th>
          </tr>
          <tr v-if="headRows != 1">
            <th v-for="column in centerSecondColumns" :style="column.style">
              {{column.label}}
            </th>
          </tr>
        </thead>
        <tbody v-tr-hight="{this: self, index: index}" v-for="(tbody, index) in data" :class="{'tr-hover': additionalSets[index]._isHover}" @mouseenter="additionalSets[index]._isHover = true" @mouseleave="additionalSets[index]._isHover = false">
          <tr :style="{height: additionalSets[index]._height + 'px'}" track-by="$index">
            <td v-for="column in centerBaseColumns" :align="column.align || 'center'" :rowspan="rowspanKey && tbody[rowspanKey].length || 1">
              <cell :row="tbody" :column="column" :index="index"></cell>
            </td>
            <td v-if="rowspanKey" v-for="column in centerRowsColumns" :align="column.align || 'center'">
              <cell :row="tbody[rowspanKey][0]" :column="column" :index="index"></cell>
            </td>
          </tr>
          <tr :style="{height: additionalSets[index]._height + 'px'}" v-for="(rowspanTr, rowspanTrIndex) in tbody[rowspanKey] " v-if="rowspanKey && rowspanTrIndex != 0">
            <td v-for="column in centerRowsColumns" :align="column.align || 'center'">
              <cell :row="rowspanTr" :column="column" :index="index"></cell>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <table ref="rightTable" class="complex-entity-table complex-right-table pull-right complex-table complex-table-striped complex-table-bordered">
      <colgroup>
        <col v-for="(column, index) in rightBaseColumns" :width="column.width" >
      </colgroup>
      <thead>
        <tr>
          <th v-for="column in rightBaseColumns" :rowspan="headRows" :colspan="column.colspans && column.colspans.length" :style="column.style">
            {{column.label}}
          </th>
        </tr>
        <tr v-if="headRows != 1">
        </tr>
      </thead>

      <tbody v-tr-hight="{this: self, index: index}" v-for="(tbody, index) in data" :class="{'tr-hover': additionalSets[index]._isHover}" @mouseenter="additionalSets[index]._isHover = true" @mouseleave="additionalSets[index]._isHover = false">
        <tr :style="{height: additionalSets[index]._height + 'px'}" track-by="$index">
          <td v-for="column in rightBaseColumns" :align="column.align || 'center'" :rowspan="rowspanKey && tbody[rowspanKey].length || 1">
            <cell :row="tbody" :column="column" :index="index"></cell>
          </td>
        </tr>
        <tr :style="{height: additionalSets[index]._height + 'px'}" track-by="$index" v-for="rows in tbody[rowspanKey] && tbody[rowspanKey].length" v-if="rows != 1">
        </tr>
      </tbody>
    </table>

  </div>

</template>

<script>
import Optiscroll from 'optiscroll'
import 'optiscroll/dist/optiscroll.css'
import Cell from './Cell'
export default {
  components: { Cell },
  props: {
    context: {
      type: Object
    },
    data: {
      type: Array
    },
    columns: {
      type: Array
    },
    rowspanKey: {
      type: String,
      default: ''
    },
    selectList: {
      type: Array,
      default: () => {
        return []
      }
    }
  },
  data () {
    return {
      self: this,

      additionalSets: [],

      optiscroll: null,
      headRows: 1,
      leftBaseColumns: [],
      rightBaseColumns: [],
      centerFirstColumns: [],
      centerSecondColumns: [],
      centerBaseColumns: [],
      centerRowsColumns: [],
      currentContext: this.context,

      centerTableStyle: {}
    }
  },
  created () {
    this.splitColumns()
    this.initAdditionalSets()
    this.setAllCheck(false)
    if (this.selectList) this.setCrtChecked()
    // console.log(this.data)
  },
  mounted () {
    this.setScroll()
  },
  watch: {
    data () {
      // console.log('dataWatch')
      this.$nextTick(() => {
        this.setScroll()
      })
      this.initAdditionalSets()
      this.setAllCheck(false)
      if (this.selectList) this.setCrtChecked()
    },
    selectList () {
      this.setCrtChecked()
    }
  },

  beforeDestory () {
    try {
      this.optiscroll.destroy()
    } catch (err) {
      console.log(err)
    }
  },

  directives: {
    'tr-hight': {
      inserted (el, binding, vnode) {
        setTimeout(() => {
          let that = binding.value.this
          let len = el.children.length
          let realHeight = Math.floor(el.offsetHeight / len)
          // console.log('inserted')
          if (realHeight > that.additionalSets[binding.value.index]._height) {
            that.additionalSets[binding.value.index]._height = realHeight
          }
        }, 0)
      }
    }
  },

  methods: {
    setScroll () {
      this.$nextTick(() => {
        let leftTableWidth = this.$refs.leftTable.offsetWidth
        let rightTableWidth = this.$refs.rightTable.offsetWidth
        let centerTableStyle = {
          left: leftTableWidth + 'px',
          right: rightTableWidth + 'px'
        }
        this.centerTableStyle = centerTableStyle
        this.$nextTick(() => {
          let centerTableScrollWidth = this.$refs.centerTableScroll.offsetWidth
          let labelWordsCount = 0
          this.centerSecondColumns.forEach(col => {
            labelWordsCount += col.label.length
          })
          let wordsWidth = labelWordsCount * 36
          if (centerTableScrollWidth > wordsWidth) {
            this.$refs.centerTable.style.minWidth = centerTableScrollWidth + 'px'
          } else {
            this.$refs.centerTable.style.minWidth = wordsWidth + 'px'
          }

          if (this.optiscroll) {
            try {
              this.optiscroll.update()
            } catch(err) {
              console.log(err)
            }
          } else {
            try {
              this.optiscroll = new Optiscroll(this.$refs.centerTableScroll, {preventParentScroll: true })
            } catch(err) {
              console.log(err)
            }
          }
        })
      })
    },

    getTrHeight (index) {
      console.log(index)
    },
    splitColumns () {
      let leftBaseColumns = []
      let centerFirstColumns = []
      let centerSecondColumns = []
      let centerBaseColumns = []
      let centerRowsColumns = []
      let rightBaseColumns = []
      this.columns.forEach(col => {
        if (col.colspans) this.headRows = 2
        if (col.fixed && col.fixed == 'left') {
          leftBaseColumns = leftBaseColumns.concat(col.colspans || col)
        } else if (col.fixed && col.fixed == 'right') {
          rightBaseColumns = rightBaseColumns.concat(col.colspans || col)
        } else {
          centerFirstColumns.push(col)
          centerSecondColumns = centerSecondColumns.concat(col.colspans || [])
          if (col.key != this.rowspanKey) {
            centerBaseColumns = centerBaseColumns.concat(col.colspans || col)
          } else {
            centerRowsColumns = centerRowsColumns.concat(col.colspans || col)
          }
        }
      })
      this.centerFirstColumns = centerFirstColumns
      this.centerSecondColumns = centerSecondColumns
      this.centerBaseColumns = centerBaseColumns
      this.centerRowsColumns = centerRowsColumns
      this.leftBaseColumns = leftBaseColumns
      this.rightBaseColumns = rightBaseColumns
    },

    initAdditionalSets () {
      let dataLen = this.data.length
      this.additionalSets = []
      for (let i = 0; i < dataLen; i++) {
       this.additionalSets.push({
          _isHover: false,
          _height: 40
        })
      }
    },

    setCrtChecked () {
      if (this.selectList.length === 0) {
        this.data.forEach((item) => {
          item.checked = false
        })
      } else {
        this.selectList.forEach((checkIndex) => {
          this.data[checkIndex].checked = true
        })
      }
    },
    setAllCheck (checked) {
      this.data.forEach(item => {
        this.$set(item, 'checked', checked)
      })
    },
    toggleCheck (tr) {
      tr.checked = !tr.checked
      let checkedList = this.data.map((item, index) => {
        if (item.checked) return index
      }).filter(checked => typeof checked !== 'undefined')
      // console.log('checkedList', checkedList)
      if (checkedList.length == this.data.length) {
        this.$emit('input', checkedList)
        this.$emit('selectedAll', checkedList)
        // this.$emit('checkIt', checkedList)
      } else {
        this.$emit('input', checkedList)
        // this.$emit('checkIt', checkedList)
      }
    }
  }
}
</script>

<style>
.optiscroll-htrack {
  height: 10px !important;
  background: rgba(0,0,0,.2) !important;
}
.complex-table {
  border-collapse: collapse;
  border-spacing: 0;
  empty-cells: show;
  border: 1px solid #e7e7e7;
  border-width: 1px 0 1px 0;
}

.complex-table caption {
  color: #000;
  font: italic 85%/1 arial, sans-serif;
  padding: 1em 0;
  text-align: center;
}

.complex-table td,
.complex-table th {
  border-left: 1px solid #e7e7e7;
  border-width: 0 0 0 1px;
  font-size: inherit;
  margin: 0;
  overflow: visible;
  padding: 0 .5em;
}
.complex-table td:first-child,
.complex-table th:first-child {
  border-left-width: 0;
}
.complex-table thead {
  background-color: #fef3f6;
  color: #000;
  text-align: left;
  vertical-align: bottom;
}

.complex-table tbody {
  background-color: #fff;
}
.complex-table-odd td {
  background-color: #f9f9f9;
}

.complex-table-striped tbody:nth-child(2n) {
  background-color: #fef3f6;
}
.complex-table-bordered td {
  border-bottom: 1px solid #e7e7e7;
}
.complex-table-bordered thead tr {
  border-bottom: 1px solid #e7e7e7;
}
.complex-table-bordered tbody > tr:last-child > td {
  border-bottom-width: 0;
}

.complex-table-horizontal td,
.complex-table-horizontal th {
  border-width: 0 0 1px 0;
  border-bottom: 1px solid #eff3f6;
}
.complex-table-horizontal tbody > tr:last-child > td {
  border-bottom-width: 0;
}

.complex-table-all {
  width: 100%;
}
.complex-left-table {
  top: 0;
  left: 0;
  box-shadow: 1px 0 4px 2px rgba(0,0,0,.1);
  z-index: 99;
}
.complex-right-table {
  position: relative;
  box-shadow: -1px 0 4px 2px rgba(0,0,0,.1);
  z-index: 99;
}
.complex-center-table-scroll {
  position: absolute!important;
  top: 0;
  bottom: -1px;
}
.complex-entity-table {
  color: #666;
  border: 1px solid #eee;
}
.complex-entity-table tr {
  height: 42px;
}
.complex-entity-table thead tr th {
  color: #666;
  vertical-align: middle;
  font-weight: normal;
  text-align: center;
  font-size: .9em;
  padding: 0;
}

.complex-entity-table tbody.tr-hover {
  background: #ebf7ff;
}

</style>
