<template>
  <div ref="cell" class="over-words">
    <template v-if="renderType === 'normal'">
      <span>{{row[column.key]}}</span>
    </template>
    <template v-if="renderType === 'check'">
      <div class="select-grid position-relateve">
        <input :id="'xCheck' + index" type="checkbox" :checked="row.checked" @change="selectThis" class="position-absolute theCheckbox">
        <label :for="'xCheck' + index" class="checkbox-label" :class="{'check-active': row.checked}"></label>
      </div>
    </template>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  name: 'TableCell',
  data () {
    return {
      self: this,
      renderType: '',
      context: this.$parent.currentContext
    }
  },
  props: {
    row: Object,
    column: Object,
    index: Number
  },
  created () {
    // console.log(111)
    if (this.column.type === 'check') {
      this.renderType = 'check'
    } else if (this.column.render) {
      this.renderType = 'render'
    } else {
      this.renderType = 'normal'
    }
  },
  mounted () {
    this.$nextTick(() => {
      this.compile()
    })
  },
  methods: {
    compile () {
      if (this.column.render) {
        const $parent = this.context
        const template = this.column.render(this.row, this.column, this.index)
        const cell = document.createElement('div')
        cell.innerHTML = template
        this.$el.innerHTML = ''
        let methods = {}
        Object.keys($parent).forEach(key => {
          const func = $parent[key]
          if (typeof func === 'function' && (func.name === 'boundFn' || func.name === 'n')) {
            methods[key] = func
          }
        })
        const res = Vue.compile(cell.outerHTML)
        const components = {}
        Object.getOwnPropertyNames($parent.$options.components).forEach(item => {
          components[item] = $parent.$options.components[item]
        })
        const component = new Vue({
          render: res.render,
          staticRenderFns: res.staticRenderFns,
          methods: methods,
          data () {
            return $parent._data
          },
          components: components
        })
        component.row = this.row
        component.column = this.column
        const Cell = component.$mount()
        this.$refs.cell.appendChild(Cell.$el)
      }
    },
    selectThis () {
      this.$parent.toggleCheck(this.row)
    }
  },
  watch: {
    row: {
      handler: function (val, oldVal) {
        this.$nextTick(() => {
          this.compile()
        })
      },
      deep: true
    }
  }
}
</script>

<style scoped>
  .over-words {
    max-width: 9rem;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    -webkit-line-clamp: 2;
    font-size: .9rem;
  }
  .select-grid {
    width: 2rem;
  }
  .theCheckbox {
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    z-index: -1;
    visibility: hidden;
  }
  .checkbox-label {
    display: inline-block;
    width: 18px;
    height: 18px;
    border: 0;
    background: url("../../assets/imgs/checkbox.gif") -46px 0;
    vertical-align: text-top;
  }
  .checkbox-label:hover { background: url("../../assets/imgs/checkbox.gif") 0 0; }
  .checkbox-label.check-active { background: url("../../assets/imgs/checkbox.gif") -24px 0; }

</style>
