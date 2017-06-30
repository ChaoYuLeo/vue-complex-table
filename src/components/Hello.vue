<template>
  <div class="hello">
    <complex-table :context="self" :columns="columns" :data="data" :rowspan-key="rowspanKey" />
  </div>
</template>

<script>
import ComplexTable from '@/components/complexTable/ComplexTable.vue'
export default {
  name: 'hello',
  components: { ComplexTable },
  data () {
    return {
      tableKey: 1,
      self: this,
      columns: [
        {
          label: '选择',
          key: 'check',
          fixed: 'left',
          type: 'check'
        },
        {
          label: '姓名',
          key: 'name',
          fixed: 'left'
        },
        {
          label: '测试竖列合并1',
          key: 'rowspan'
        },
        {
          label: '基本信息',
          key: 'baseInfo',
          colspans: [
            {
              label: '编号',
              key: 'id',
              width: '80'
            },
            {
              label: '基本1',
              key: 'base1'
            },
            {
              label: '基本2',
              key: 'base2'
            },
            {
              label: '基本3',
              key: 'base3'
            },
            {
              label: '基本4',
              key: 'base4'
            },
            {
              label: '基本5',
              key: 'base5'
            },
            {
              label: '基本6',
              key: 'base6'
            },
            {
              label: '基本7',
              key: 'base7'
            },
            {
              label: '基本8',
              key: 'base8'
            },
          ]
        },
        {
          label: '测试竖列合并2',
          key: 'rowspan2'
        },
        {
          label: '配套信息',
          key: 'setInfo',
          colspans: [
            {
              label: '配套1',
              key: 'set1',
              align: 'left',
              render (row, column, index) {
                return `<p style="color: red;">${row.set1}</p>`
              }
            },
            {
              label: '配套2',
              key: 'set2'
            },
            {
              label: '配套3',
              key: 'set3'
            },
            {
              label: '配套4',
              key: 'set4'
            },
            {
              label: '配套5',
              key: 'set5'
            },
            {
              label: '配套6',
              key: 'set6'
            },
            {
              label: '配套7',
              key: 'set7'
            },
            {
              label: '配套8',
              key: 'set8'
            },
            {
              label: '配套9',
              key: 'set9'
            },
            {
              label: '配套10',
              key: 'set10'
            },
            {
              label: '配套11',
              key: 'set11'
            },
            {
              label: '配套12',
              key: 'set12'
            }
          ]
        },
        {
          label: 'right1',
          key: 'right1',
          width: '60',
          fixed: 'right'
        },
        {
          label: 'right2(custom)',
          key: 'right2',
          fixed: 'right',
          render (row, column, index) {
            return `<span style="color: red;font-size:20px;">${index}</span>`
          }
        }
      ],
      rowspanKey: 'setInfo',
      data: [
        {
          name: 'Leo',
          left1: 'left1-1',
          rowspan: '5',
          rowspan2: '7',
          id: '1',
          base1: '基本1',
          base2: '基本2',
          base3: '基本3',
          base4: '基本4',
          setInfo: [
            {
              set1: 'set1-1',
              set2: 'set2-2'
            },
            {
              set1: 'set1-2',
              set2: 'set2-2'
            },
            {
              set1: 'set1-3',
              set2: 'set2-3'
            },
            {
              set1: 'set1-4',
              set2: 'set4'
            },
            {
              set1: 'set1-5',
              set2: 'set5'
            }
          ],
          right1: 'r11',
          right2: 'r12'
        },
        {
          name: 'Jerry',
          check: '2',
          left1: 'left1-2',
          rowspan: '6',
          rowspan2: '7',
          id: '2',
          base1: '基本2-1',
          base2: '基本2-2',
          base3: '基本2-3',
          base4: '基本2-4',
          setInfo: [
            {
              set1: 'set1',
              set2: 'set2'
            },
            {
              set1: 'set3',
              set2: 'set4'
            }
          ],
          right1: 'r21',
          right2: 'r22'
        }
      ]
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
