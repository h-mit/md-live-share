<template lang="pug">
#app
  el-dialog(
    title="ログイン"
    :visible="!isLoggedIn"
    width="30%"
    :close-on-click-modal="false"
    :close-on-press-escape="false"
    :show-close="false"
  )
    el-form(
      :model="form"
      @submit.native.prevent="login"
    )
      el-form-item(label="ユーザー名")
        el-input(v-model="form.username" auto-complete="off")
    span(slot="footer")
      el-button(
        type="primary"
        :disabled="!isAbleToLogin"
        @click="login"
      ) ログイン
  el-container
    el-header
      span.title Markdown Live Share
    el-container
      el-main
        MarkdownEditor(
          :isLoggedIn="isLoggedIn"
        )
      el-aside(width="150px")
</template>

<script>
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
      }
    }
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
      if (this.form.username) {
        this.username = this.form.username
      }
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
