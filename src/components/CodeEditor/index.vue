<template>
  <div ref="diffContainer" class="the-code-diff-editor-container" @change="change" />
</template>

<script>
import * as monaco from 'monaco-editor/esm/vs/editor/editor.api'
import 'monaco-editor/esm/vs/basic-languages/shell/shell.contribution'
import 'monaco-editor/esm/vs/basic-languages/sql/sql.contribution'
import 'monaco-editor/esm/vs/basic-languages/python/python.contribution'
import 'monaco-editor/esm/vs/editor/contrib/find/findController.js'

export default {
  props: {
    language: {
      type: String,
      default: 'ini'
    },
    // eslint-disable-next-line vue/require-default-prop
    text: String
  },
  data() {
    return {
      monacoDiffInstance: null
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    change() {
      console.log('yes')
    },
    init() {
      // 初始化编辑器实例
      console.log(this.$refs)
      this.monacoDiffInstance = monaco.editor.create(this.$refs['diffContainer'], {
        theme: 'vs  ', // vs, hc-black, or vs-dark
        readOnly: false
      })
      this.language = 'ini'
      var model = monaco.editor.createModel(this.text, this.language)
      this.monacoDiffInstance.setModel(model)
    }
  }
}
</script>

<style scoped>
.the-code-diff-editor-container {
    width: 100%;
    height: 200px;
    /* overflow: auto; */
  }
.the-code-diff-editor-container .monaco-editor .scroll-decoration {
   box-shadow: none;
}
</style>
