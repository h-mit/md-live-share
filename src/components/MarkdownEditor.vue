<template lang="pug">
.markdown-editor
  textarea.editor(
    :value="mdText"
    @input="updateText"
    spellcheck="false"
  )
  div.preview.markdown-body(
    v-html="previewText"
  )
</template>

<script>
import _ from 'lodash'
import marked from 'marked'
import hljs from 'highlight.js'
import 'github-markdown-css/github-markdown.css'
import 'highlight.js/styles/atom-one-dark.css'

marked.setOptions({
  breaks: true,
  sanitize: true,
  highlight: function (code) {
    return hljs.highlightAuto(code).value
  }
})

export default {
  name: 'MarkdownEditor',
  props: {
    isLoggedIn: {
      type: Boolean,
      required: true
    }
  },
  data () {
    return {
      mdText: '# sample\n'
    }
  },
  computed: {
    previewText () {
      return marked(this.mdText)
    }
  },
  methods: {
    updateText: _.debounce(function (e) {
      console.log(e.target.value)
      this.mdText = e.target.value
    }, 300)
  }
}
</script>

<style scoped>
.markdown-editor {
  display: flex;
  height: 100%;
}

.editor {
  flex: 1;
  padding: 15px;
  border: none;
  border-right: 1px solid #ccc;
  background: #282c34;
  color: #ccc;
  resize: none;
  outline: none;
  font-family: 'MyricaM M', 'Ricty Diminished', Consolas, 'Courier New', Courier, Monaco, monospace;
  font-size: 15px;
  line-height: 1.4;
}

.preview {
  flex: 1;
  padding: 15px;
  font-family: 'Hiragino Kaku Gothic ProN', 'ヒラギノ角ゴ ProN W3', Meiryo, メイリオ, Osaka, 'MS PGothic', arial, helvetica, sans-serif;
  font-size: .9em;
  overflow: auto;
}
</style>
