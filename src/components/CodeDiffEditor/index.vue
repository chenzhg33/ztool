<template>
  <div ref="diffContainer" class="the-code-diff-editor-container" />
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
      default: 'json'
    },
    // eslint-disable-next-line vue/require-default-prop
    oldValue: String,
    // eslint-disable-next-line vue/require-default-prop
    value: String
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
    init() {
      // 初始化编辑器实例
      this.monacoDiffInstance = monaco.editor.createDiffEditor(this.$refs['diffContainer'], {
        theme: 'vs-dark', // vs, hc-black, or vs-dark
        readOnly: true
      })
      this.monacoDiffInstance.setModel({
        original: monaco.editor.createModel(this.oldValue, this.language),
        modified: monaco.editor.createModel(this.value, this.language)
      })
    }
  }
}
</script>

<style scoped>
.the-code-diff-editor-container {
    width: 100%;
    height: 100%;
    overflow: auto;
  }
.the-code-diff-editor-container .monaco-editor .scroll-decoration {
   box-shadow: none;
}
</style>
