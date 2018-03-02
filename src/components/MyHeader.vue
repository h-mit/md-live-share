<template lang="pug">
.my-header
  //- ログインダイアログ
  el-dialog(title="友達を招待" :visible.sync="dialogVisible" width="30%")
    span {{ message }}
    el-form(:inline="true")
      el-form-item: el-input(:value="sharedLink" auto-complete="off")
      el-form-item: el-button.copy(type="primary" :data-clipboard-text="sharedLink") Copy
  //- ヘッダーコンテンツ
  .header-content
    span.title Markdown Live Share
    .link-container(v-if="roomName")
      el-button(type="primary" @click="openDialog") Invite People
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
  data () {
    return {
      dialogVisible: false,
      message: 'リンクを共有してこの部屋へ招待します。'
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
  },
  methods: {
    openDialog () {
      this.dialogVisible = true
    }
  }
}
</script>

<style scoped>
.header-content {
  display: flex;
  color: #555;
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
</style>
