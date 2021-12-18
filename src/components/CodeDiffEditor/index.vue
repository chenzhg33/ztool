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
      default: 'ini'
    },
    // eslint-disable-next-line vue/require-default-prop
    oldValue: String,
    // eslint-disable-next-line vue/require-default-prop
    newValue: String
  },
  data() {
    return {
      monacoDiffInstance: null,
      oldModel: null,
      newModel: null
    }
  },
  watch: {
    oldValue: function() {
      // this.doDiff()
    },
    newValue: function() {
      // this.doDiff()
    }
  },
  mounted() {
  },

  methods: {
    doDiff() {
      // 初始化编辑器实例
      if (this.monacoDiffInstance) {
        this.monacoDiffInstance.dispose()
      }
      this.monacoDiffInstance = monaco.editor.createDiffEditor(this.$refs['diffContainer'], {
        // theme: 'vs-dark', // vs, hc-black, or vs-dark
        readOnly: false
      })
      this.language = 'ini'
      if (this.oldModel) {
        this.oldModel.dispose()
      }
      this.oldModel = monaco.editor.createModel(this.oldValue, this.language)
      if (this.newModel) {
        this.newModel.dispose()
      }
      this.newModel = monaco.editor.createModel(this.newValue, this.language)
      this.monacoDiffInstance.setModel({
        original: this.oldModel,
        modified: this.newModel
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
