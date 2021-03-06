<template lang="pug">
  .wu-cell.wu-field
    .wu-cell__bd {{label}}
    .wu-cell__ft
      textarea.wu-field__control(v-if="type === 'textarea'", v-bind="$attrs", v-on="listeners", ref="textarea", :value="value")
      input.wu-field__control(v-else="", v-bind="$attrs", v-on="listeners", :type="type", :placeholder="placeholder", :value="value")
</template>

<script>
import { isObj } from '@/utils'

export default {
  name: 'wuField',

  inheritAttrs: false,

  props: {
    type: {
      type: String,
      default: 'text'
    },
    value: {},
    label: String,
    placeholder: String,
    error: Boolean,
    border: Boolean,
    required: Boolean,
    autosize: [Boolean, Object]
  },

  watch: {
    value  () {
      this.$nextTick(this.adjustSize)
    }
  },

  mounted () {
    this.$nextTick(this.adjustSize)
  },

  computed: {
    listeners () {
      return {
        ...this.$listeners,
        input: this.onInput,
        keypress: this.onKeypress
      }
    }
  },

  methods: {
    onInput (event) {
      this.$emit('input', event.target.value)
    },

    onKeypress (event) {
      if (this.type === 'number') {
        const { keyCode } = event
        const allowPoint = this.value.indexOf('.') === -1
        const isValidKey = (keyCode >= 48 && keyCode <= 57) || (keyCode === 46 && allowPoint)
        if (!isValidKey) {
          event.preventDefault()
        }
      }
      this.$emit('keypress', event)
    },

    adjustSize () {
      if (!(this.type === 'textarea' && this.autosize)) {
        return
      }

      const el = this.$refs.textarea
      if (!el) {
        return
      }

      el.style.height = 'auto'

      let height = el.scrollHeight
      if (isObj(this.autosize)) {
        const { maxHeight, minHeight } = this.autosize
        if (maxHeight) {
          height = Math.min(height, maxHeight)
        }
        if (minHeight) {
          height = Math.max(height, minHeight)
        }
      }

      el.style.height = height + 'px'
    }
  }
}
</script>
