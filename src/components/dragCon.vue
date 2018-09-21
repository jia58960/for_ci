<template>
    <div :style="`bottom:${bottom}px;right:${right}px;`" v-show="innerShow" ref="note" class="note-editor-box">
      <slot name="content"></slot>
    </div>
</template>

<script>
export default {
  data: function() {
    return {
      right: 30,
      bottom: 30,
      innerCOntent: '',
      innerShow: this.editorShow,
      innerId: this.editorId
    }
  },
  model: {
    prop: 'editorShow',
    event: 'change'
  },
  watch: {
    editorShow: function(val) {
      this.innerShow = val
      document.body.appendChild(this.$refs.note)
      this.$refs.note.onselectstart = function(event) {
        event.returnValue = false
      }
      const noteDom = this.$refs.note
      if (val) {
        this.$nextTick(() => {
          noteDom.onmousedown = down => {
            const nodrag = down.target.getAttribute('nodrag')
            if (nodrag === 'true') {
              return
            }
            // if (down.)
            const disX = down.clientX
            const disY = down.clientY
            const right = document.body.offsetWidth - noteDom.offsetLeft - noteDom.offsetWidth
            const bottom = window.innerHeight - noteDom.offsetTop - noteDom.offsetHeight
            const left = noteDom.offsetLeft
            const top = noteDom.offsetTop
            window.onmousemove = move => {
              const moveX = move.clientX - disX
              const moveY = move.clientY - disY
              console.log('moveX:' + moveX, 'moveY:' + moveY)
              if ((right - moveX) < 0 || (bottom - moveY) < 0 || (left + moveX) < 0 || (top + moveY) < 0) {
                return
              }
              noteDom.style.right = right - moveX + 'px'
              noteDom.style.bottom = bottom - moveY + 'px'
            }
            window.onmouseup = up => {
              window.onmousemove = null
              window.onmouseup = null
            }
          }
        })
      } else {
        this.$nextTick(() => {
          noteDom.style.right = '30px'
          noteDom.style.bottom = '30px'
        })
      }
    }
  },
  props: {
    editorId: {
      type: String,
      default: ''
    },
    editorShow: {
      type: Boolean,
      default: false
    }
  },
  methods: {
  }
}
</script>
<style lang="scss" scoped>

</style>
