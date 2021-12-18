<template>
  <div ref="diffContainer" @change="change" />
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
      monacoDiffInstance: null,
      model: null
    }
  },
  watch: {
    text: function() {
      this.setText(this.text)
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    change() {
      var content = this.model.getValue()
      console.log(content)
      this.$emit('contentChannge', content)
    },

    setText(text) {
      this.model.setValue(text)
      // this.monacoDiffInstance.setModel(this.model)
    },
    init() {
      // 初始化编辑器实例
      console.log('hahahah1')
      this.monacoDiffInstance = monaco.editor.create(this.$refs['diffContainer'], {
        theme: 'vs  ', // vs, hc-black, or vs-dark
        readOnly: false
      })
      this.language = 'ini'
      this.model = monaco.editor.createModel(this.text, this.language)
      this.monacoDiffInstance.setModel(this.model)
      this.model.onDidChangeContent((event) => {
        this.change()
      })
    }
  }
}
</script>

<style scoped>
.the-code-diff-editor-container {
    width: 50%;
    /* overflow: auto; */
  }
.the-code-diff-editor-container .monaco-editor .scroll-decoration {
   box-shadow: none;
}
</style>
