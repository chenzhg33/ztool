<template>
  <div>
    <div>
      <el-button type="success" @click="contentEdit">返回编辑</el-button>
      <el-button type="success" @click="contentDiff">对比</el-button>
    </div>

    <div v-show="isDiff == false" style="padding:30px;">
      <CodeEditor class="leftEditor" :text="editorText" @contentChannge="leftContentChannge" />
      <CodeEditor class="rightEditor" :text="rightEditorText" @contentChannge="rightContentChannge" />
    </div>

    <div style="padding:30px;">
      <CodeDiffEditor ref="codeDiffEditor" class="diffEditor" :old-value="diffOldValue" :new-value="diffNewValue" />
      </divstyle="padding:30px;"></div>
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
      diffOldValue: '',
      diffNewValue: '',
      editorText: '',
      rightEditorText: '',
      isDiff: false
    }
  },

  methods: {
    contentDiff() {
      this.isDiff = true
      console.log(this.$refs.codeDiffEditor)
      this.$refs.codeDiffEditor.doDiff()
    },
    contentEdit() {
      this.isDiff = false
    },
    leftContentChannge(text) {
      // this.editorText = text
      this.diffOldValue = text
    },
    rightContentChannge(text) {
      // this.rightEditorText = text
      this.diffNewValue = text
    }
  }
}
</script>

<style scoped>
.leftEditor, .rightEditor {
  width: 50%;
  height: 800px;
  border:1px solid #cccccc;
  display:inline-block;
}
.diffEditor {
height: 800px;
 border:1px solid #cccccc;
}
</style>
