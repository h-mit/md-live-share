<template lang="pug">
#app
  //- ログインダイアログ
  el-dialog(title="ログイン" :visible="!isLoggedIn" width="30%" :close-on-click-modal="false" :close-on-press-escape="false" :show-close="false")
    el-form(:model="form" status-icon :rules="rules" ref="form" @submit.native.prevent="login")
      el-form-item(label="ユーザー名（英数字が使用できます）" prop="username")
        el-input(v-model="form.username" auto-complete="off")
    span(slot="footer")
      el-button(type="primary" :disabled="!isAbleToLogin" @click="login") ログイン
  el-container
    el-header: span.title Markdown Live Share
    el-container
      //- メインコンテンツ
      el-main: MarkdownEditor(:isLoggedIn="isLoggedIn")
      el-aside(width="150px")
</template>

<script>
import Peer from 'skyway-js'
import key from './skyway-key'
import shortid from 'shortid'
import MarkdownEditor from './components/MarkdownEditor'

export default {
  name: 'App',
  components: {
    MarkdownEditor
  },
  data () {
    return {
      username: '',
      form: {
        username: ''
      },
      rules: {
        username: [
          {
            validator (rule, value, callback) {
              if (!value) {
                callback(new Error('ユーザー名を入力してください'))
              } else if (!/^[A-Za-z0-9]+$/.exec(value)) {
                callback(new Error('使用できない文字が含まれています'))
              } else {
                callback()
              }
            },
            trigger: 'blur'
          }
        ]
      },
      uuid: '',
      room: null
    }
  },
  created () {
    this.uuid = shortid.generate()
  },
  computed: {
    isLoggedIn () {
      return !!this.username
    },
    isAbleToLogin () {
      return !!this.form.username
    }
  },
  methods: {
    login () {
      this.$refs.form.validate(valid => {
        if (valid) {
          // バリデーションを通過した場合のみ処理
          this.username = this.form.username
          this.setupPeer()
        }
      })
    },
    // Peerオブジェクトを生成する
    setupPeer () {
      const peerId = this.username + ' ' + this.uuid
      const peer = new Peer(peerId, {
        key,
        debug: 3
      })
      peer
        .on('open', id => {
          console.log(id)
        })
        .on('connection', connection => {
          this.connect(connection)
        })
        .on('error', this.handleError)
    },
    connect (connection) {
      console.log(connection)
    },
    // メッセージの表示
    message (message, type = 'info') {
      this.$message({
        showClose: true,
        message,
        type
      })
    },
    // 警告メッセージの表示
    warning (message) {
      this.message(message, 'warning')
    },
    // エラーハンドリング
    handleError (error) {
      this.message(error.message, 'error')
    }
  }
}
</script>

<style>
html, body, #app, .el-container {
  margin: 0;
  height: 100%;
}

#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.el-header {
  background-color: #3F51B5;
  color: #eee;
  line-height: 60px;
}

.el-aside {
  background-color: #bbb;
  color: #333;
}

.el-main {
  padding: 0;
}

.title {
  font-family: 'Poiret One', cursive;
  font-size: 20px;
  font-weight: bold;
  letter-spacing: .2em;
}
</style>
