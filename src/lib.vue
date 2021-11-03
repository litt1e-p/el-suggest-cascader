<template>
  <div class="_el__suggest-cc">
    <el-cascader class="wpr100"
                v-model="chos"
                @change="_sl7"
                :placeholder="placeholder"
                :options="data"
                clearable
                :collapse-tags="collapseTags"
                :show-all-levels="false"
                :key="Math.random()"
                :props="props"
                filterable
                :popper-class="`${suggestions && suggestions.length ? '_el__suggest-cc-ts' + suggestions.length : ''} _el__suggest-cc-main`">
      <template slot-scope="{node, data}">
        <div>
          <div v-if="!data.suggestions">
            <span>{{ data.label }}</span>
            <span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
          </div>
          <div v-else class="ps-ar">
            <div class="ps-ar-tl sl" v-if="_arb(data.suggestions, hideEmpty)">{{data.label}}</div>
            <div class="ps-ar-ct" v-if="_arb(data.suggestions, hideEmpty)">
              <el-tooltip :disabled="!i['tips']" placement="top" v-for="i in data.suggestions" :key="i.label">
                <template #content>
                  <div class="ps-ar-tp" v-html="i['tips']"></div>
                </template>
                <span :class="`${_sed0(i, data) ? 'ps-ar-op-ck' : ''} ps-ar-op`" @click.stop="_sl8(i[props.value || 'value'], data)">{{ i[props.label || 'label'] }}</span>
              </el-tooltip>
            </div>
            <div class="ps-ar-bp sl" v-if="data._temind && _arb(data.suggestions, hideEmpty)">{{optionsTitle}}</div>
          </div>
        </div>
      </template>
    </el-cascader>
  </div>
</template>

<script>

import { cloneDeep } from 'lodash'

export default {
  name: 'ElSuggestCascader',
  props: {
    value: {
      type: [String, Array]
    },
    suggestions: {
      type: Array,
      default: () => Array.prototype.constructor()
    },
    options: {
      type: Array,
      default: () => Array.prototype.constructor()
    },
    props: {
      type: Object,
      default: () => ({ expandTrigger: 'hover', label: 'label', value: 'value', emitPath: true, multiple: false })
    },
    placeholder: {
      type: String,
      default: () => 'click to select'
    },
    optionsTitle: {
      type: String,
      default: () => 'all options'
    },
    collapseTags: {
      type: Boolean,
      default: () => true
    },
    hideEmpty: {
      type: Boolean,
      default: () => true
    },
    debug: {
      type: Boolean,
      default: () => false
    }
  },
  model: {
    prop: 'value',
    event: 'change'
  },
  data () {
    return {
      chos: void 0,
      data: void 0
    }
  },
  mounted () {
    this._bd4(true)
  },
  methods: {
    _tmd (d) {
      let rs = d
      if (!this._arb(d)) {
        return rs
      }
      let l = d.length - 1
      while (l >= 0) {
        if (this._arb(d[l]['suggestions'])) {
          d[l]['_temind'] = true
          break
        }
        l--
      }
      return d
    },
    _bd4 (bl = false) {
      this.data = this._tmd(this.suggestions).concat(this.options)
      if (bl && this.value) {
        this._bi3(this.value)
      }
    },
    _bi3 (v, d) {
      this.chos = d ? ['_unlock', cloneDeep(v)] : cloneDeep(v)
      this._c01(this.chos)
    },
    _sl8 (i, d) {
      let sl
      if (this.props.multiple) {
        if (this._sed0(i, d)) {
          sl = this._se6(i, this.options, cloneDeep(this.chos), false)
        } else {
          sl = this.props.emitPath ? this._se6(i, this.options, cloneDeep(this.chos)) : Array.isArray(this.chos) ? this.chos.concat([i]) : [i]
        }
      } else {
        if (this._sed0(i, d)) {
          return
        }
        sl = this.props.emitPath ? (d[this.props.value || 'value'] ? [d[this.props.value || 'value'], i] : i) : i
      }
      this.$nextTick(() => {
        this._bi3(sl, true)
      })
    },
    _se6 (v, d, x, a = true) {
      const { path, hitted } = this._d0sp.apply(this, arguments)
      if (!hitted || path.length <= 0) {
        return x
      }
      if (!a) {
        return x.filter(i => !i.includes(v))
      }
      if (x && Array.isArray(x)) {
        x.push(path.reverse())
        return x
      }
      return [path.reverse()]
    },
    _d0sp (v, d) {
      let rs = {
        path: [],
        hitted: false
      }
      if (!d) {
        return rs
      }
      if (Array.isArray(d)) {
        for (let i = 0, l = d.length; i < l; i++) {
          const { path, hitted } = this._d0sp(v, d[i])
          if (hitted) {
            rs.path = rs.path.concat(path)
            rs.hitted = hitted
            break
          }
        }
      } else {
        if (v === d[this.props.value || 'value']) {
          rs['path'].push(d[this.props.value || 'value'])
          rs['hitted'] = true
        } else {
          if (d['children'] && d['children'].length) {
            rs = this._d0sp(v, d['children'])
          }
          rs['path'].push(d[this.props.value || 'value'])
        }
      }
      return rs
    },
    _arb (v, l = true) {
      return v && Object.prototype.toString.call(v) === '[object Array]' && (l ? v.length > 0 : true)
    },
    _c01 (v) {
      if (this._arb(v) && v[0] === '_unlock') {
        this.$emit('change', cloneDeep(v[1]))
      }
    },
    _sed0 (i, d) {
      if (!this.chos || !this.chos.length) {
        return false
      }
      let es
      const clue = i['value'] || i
      if (this.chos && this.chos.length) {
        es = false
      }
      es = this._ctn(clue, this.chos)
      return es
    },
    _ctn (c, d) {
      if (!d || !d.length || !c || !c.length) {
        return false
      } else if (d === c) {
        return true
      } else if (d.includes(c)) {
        return true
      } else if (JSON.stringify(d).indexOf(JSON.stringify(c)) > -1) {
        return true
      }
      return false
    },
    _sl7 (v) {
      this._bi3(v, true)
    },
    _sd4 (d) {
      let rs = []
      if (!Array.isArray(d)) {
        return d
      } else {
        for (let i = 0, l = d.length; i < l; i++) {
          rs = rs.concat(this._sd4(d[i]))
        }
      }
      return rs
    }
  },
  watch: {
    value (v) {
      this._bi3(v)
    },
    suggestions (v) {
      this._bd4()
    },
    options (v) {
      this._bd4()
    }
  }
}

</script>

<style lang="scss">
@import './style/style.scss';
</style>

<style lang="scss" scoped>
$ac: #007aff;
$sb: #f0f2f5;
$sl: #409eff;
.sl {
  color: #999;
  font-size: 14px;
  line-height: 34px;
  &:before {
    content: '';
    display: inline-block;
    width: 3px;
    height: 15px;
    background-color: $sl;
    margin-right: 10px;
    vertical-align: -3px;
  }
}
.ps-ar {
  $this: &;
  .ps-ar-ct {
    display: flex;
    flex-wrap: wrap;
    justify-content: flex-start;
    align-items: center;
    .ps-ar-op {
      margin-right: 6px;
      pointer-events: auto;
      max-width: 100%;
      margin-bottom: 5px;
      white-space: nowrap;
      overflow: hidden;
      text-overflow: ellipsis;
      cursor: pointer;
      &:hover {
        color: $sl;
        font-weight: 450;
      }
    }
    .ps-ar-op-ck {
      color: $sl;
      background-color: $sb;
      font-weight: 450;
      border-radius: 9px;
      padding: 0 10px;
      height: 28px;
      line-height: 28px;
      font-size: 13px;
    }
  }
}
</style>
