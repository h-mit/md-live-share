<template lang="pug">
#app
  el-container
    el-header
      span.title Markdown Live Share
    el-container
      el-main
        MarkdownEditor
      el-aside(width="150px")
</template>

<script>
import MarkdownEditor from './components/MarkdownEditor'

export default {
  name: 'App',
  components: {
    MarkdownEditor
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
