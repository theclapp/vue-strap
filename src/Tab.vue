<template>
  <div role="tabpanel" class="tab-pane"
      v-bind:class="{hide:!show}"
      v-show="show"
      :transition="transition"
  >
    <slot></slot>
  </div>
</template>

<script>
import coerceBoolean from './utils/coerceBoolean.js'

  export default {
    props: {
      header: {
        type: String
      },
      disabled: {
        type: Boolean,
        coerce: coerceBoolean,
        default: false
      }
    },
    data() {
      return {
        index: 0,
        show: false
      }
    },
    watch: {
      "show": {
        handler: function(newVal) {
          var self = this, ev;
          ev = newVal ? "tabShow" : "tabHide";
          Vue.nextTick(function() {
            self.$broadcast(ev, self.index)
          });
        },
        immediate: true,
      },
    },
    computed: {
      show() {
        return (this.$parent.active == this.index);
      },
      transition() {
        return this.$parent.effect
      }
    },
    created() {
      this.$parent.renderData.push({
        header: this.header,
        disabled: this.disabled
      })
    },
    ready() {
      var children = this.$parent.$children
      for (var i = 0; i < children.length; i++) {
        children[i].index = i;
      }
    },
    beforeDestroy() {
      this.$parent.renderData.splice(this.index, 1);
      var children = this.$parent.$children;
      for (var i = this.index + 1; i < children.length; i++) {
        children[i].index = children[i].index - 1
      }
    },
  }
</script>

<style scoped>
  .tab-content > .tab-pane {
    display: block;
  }
</style>
