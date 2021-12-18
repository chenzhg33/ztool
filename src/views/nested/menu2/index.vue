<template>
  <div>
    <div style="padding:30px;">
      <el-input v-model="editorText" placeholder="请输入内容" />
      <CodeEditor class="editor" :text="editorText" />
    </div>

    <div style="padding:30px;">
      <el-alert :closable="false" title="menu 2" />
      <CodeDiffEditor class="editor" :old-value="old" :value="val" />
    </div>
  </div>
</template>

<script>
import CodeDiffEditor from '../../../components/CodeDiffEditor'
import CodeEditor from '../../../components/CodeEditor'
export default {
  components: {
    CodeDiffEditor,
    CodeEditor
  },
  data() {
    return {
      old: '[ip]\nasdf',
      val: '[ip]\nasdf',
      editorText: ''
    }
  }
}
</script>

<style scoped>
.editor{
  height: 500px;
}
</style>
