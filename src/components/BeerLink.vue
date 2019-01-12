<script>
export default {
  name: 'similar-beer',
  props: {
    to: {
      type: [Number,String]
    },
    src: {
      type: String
    }
  },
  render (h) {
     return h('router-link',
              {
                props: {
                  to: '/'+this.to
                },
                key: this.to
              },
                  this.$slots.default)
  }
}
</script>
<style lang="scss" scoped>
</style>