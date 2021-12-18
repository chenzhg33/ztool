<template>
  <div ref="container" style="height: 360px; width: 100%" />
</template>

<script>
import * as monaco from 'monaco-editor'

export default {
  data() {
    return {
      monacoEditor: {}
    }
  },
  mounted() {
    console.log('aaaa')
    console.log(this.$refs.container)
    // 初始化编辑器，确保dom已经渲染，dialog中要写在opened中
    this.monacoEditor = monaco.editor.create(this.$refs.container, {
      value: '内容',
      readOnly: true,
      language: 'java',
      theme: 'vs-dark'
    })
  },
  methods: {
    changeEditor() {
      // 更改editor内容
      this.monacoEditor.setValue('aaa')
      this.monacoEditor.getAction('editor.action.formatDocument')._run()
    },
    destroyEditor() {
      // 销毁编辑器
      this.monacoEditor.dispose()
    }
  }
}
</script>
