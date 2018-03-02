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
    el-header
      MyHeader(:room-name="roomName")
    el-container
      //- メインコンテンツ
      el-main: MarkdownEditor(:text="text" :isLoggedIn="isLoggedIn" @change="onChangeText")
      el-aside(width="150px")
</template>

<script>
import Peer from 'skyway-js'
import key from './skyway-key'
import shortid from 'shortid'
import MyHeader from './components/MyHeader'
import MarkdownEditor from './components/MarkdownEditor'

export default {
  name: 'App',
  components: {
    MyHeader,
    MarkdownEditor
  },
  data () {
    return {
      username: '',
      form: {
        username: 'Mitani'
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
      peer: null,
      roomName: '',
      text: '# sample\n'
    }
  },
  created () {
    this.parseQuery()
    this.uuid = shortid.generate()
  },
  computed: {
    isLoggedIn () {
      return !!this.username && !!this.roomName
    },
    isAbleToLogin () {
      return !!this.form.username
    },
    peerId () {
      return this.username + ' ' + this.uuid
    }
  },
  methods: {
    parseQuery () {
      // URLのクエリをパースしてroomNameを取得
      const pairs = location.search.substring(1).split('&')
      for (const pair of pairs) {
        const kv = pair.split('=')
        if (kv[0] === 'r') {
          this.roomName = kv[1]
        }
      }
    },
    login () {
      this.$refs.form.validate(valid => {
        if (valid) {
          // バリデーションを通過した場合のみ処理
          this.username = this.form.username
          this.setupPeer()
        }
      })
    },
    // 自分がテキストを変更したときの処理
    onChangeText (value) {
      this.text = value
      const room = this.peer.rooms[this.roomName]
      room.send(this.text)
    },
    // Peerオブジェクトを生成する
    setupPeer () {
      this.peer = new Peer(this.peerId, {
        key,
        debug: 2
      })
        .on('open', id => {
          // オープンしたら部屋を作成する
          this.joinRoom()
        })
        .on('connection', connection => {
          this.connect(connection)
        })
        .on('error', this.handleError)
    },
    // 部屋に参加する
    joinRoom () {
      if (!this.roomName) {
        // 部屋が存在しなければ自分のuuidで部屋を作成する
        this.roomName = this.uuid
      }
      const room = this.peer.joinRoom(this.roomName, { mode: 'sfu' })
        .on('open', () => {
          console.log('部屋がオープンしました')
          this.connect(room)
        })
    },
    // 部屋のイベントハンドラ設定
    connect (room) {
      room.getLog()
      room
        .once('log', logs => {
          // 途中から部屋に参加した場合はルームのログを取得して、過去の状態を復元する
          for (let log of logs) {
            log = JSON.parse(log)
            console.log(log)
            // log.messageTypeは3通りある
            // ROOM_DATA, ROOM_USER_JOIN, ROOM_USER_LEAVE
          }
        })
        .on('data', data => {
          // テキストデータを更新
          this.text = data.data
        })
        .on('peerJoin', peerId => {
          console.log(`${peerId} が部屋に参加しました`)
        })
        .on('peerLeave', peerId => {
          console.log(`${peerId} が部屋から退室しました`)
        })
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
  min-width: 500px;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.el-header {
  background-color: #f8f8f8;
  border-bottom: 1px solid #eee;
}

.el-aside {
  background-color: #bbb;
  color: #333;
}

.el-main {
  padding: 0;
}

.el-dialog__body {
  padding-top: 20px;
  padding-bottom: 10px;
}

</style>
