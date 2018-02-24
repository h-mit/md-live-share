<template lang="pug">
.my-header
  span.title Markdown Live Share
  .link-container(v-if="roomName")
    span.link-label 共有リンク
    el-input(:value="sharedLink" spellcheck="false")
    el-button.copy(type="primary" :data-clipboard-text="sharedLink") Copy to clipboard
</template>

<script>
import Clipboard from 'clipboard'

export default {
  name: 'MyHeader',
  props: {
    roomName: {
      type: String,
      required: true
    }
  },
  mounted () {
    // eslint-disable-next-line no-new
    new Clipboard('.copy')
  },
  computed: {
    sharedLink () {
      const href = location.href.split('?')[0]
      return `${href}?r=${this.roomName}`
    }
  }
}
</script>

<style scoped>
.my-header {
  display: flex;
  color: #eee;
  line-height: 60px;
}

.title {
  font-family: 'Poiret One', cursive;
  font-size: 20px;
  font-weight: bold;
  letter-spacing: .2em;
}

.link-container {
  flex: 1;
  text-align: right;
}

.link-label {
  margin-right: 10px;
  font-size: 14px;
}

.el-input {
  margin-right: 10px;
  width: 300px;
}
</style>
