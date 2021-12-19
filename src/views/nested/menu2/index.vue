<template>
  <div>
    <div style="margin:10px">
      <el-button type="info" @click="changeEditorNum">{{ bOneEditor ? '双编辑器' : '单编辑器' }}</el-button>
      <el-button type="success" @click="contentSort">SORT</el-button>
      <el-button type="success" @click="contentUnique">UNIQ</el-button>
      <el-button type="success" @click="contentEdit">返回编辑</el-button>
      <el-button type="success" @click="contentDiff">对比</el-button>
      <el-button type="success" @click="onlyShowDiff">{{ bOnlyShowDiff ? '查看全部' : '只看差异' }}</el-button>
    </div>

    <div v-show="isDiff == false">
      <CodeEditor
        ref="leftEditor"
        class="leftEditor"
        :class="{ 'full-width': bOneEditor, 'half-width' : !bOneEditor}"
        :text="editorText"
        @contentChannge="leftContentChannge"
      />
      <CodeEditor
        v-show="! bOneEditor"
        ref="rightEditor"
        class="rightEditor"
        :class="{ 'no-width': bOneEditor, 'half-width' : !bOneEditor}"
        :text="rightEditorText"
        @contentChannge="rightContentChannge"
      />
    </div>

    <!-- <div v-show="isDiff" style="padding:30px;"> -->
    <div v-show="!bOneEditor">
      <CodeDiffEditor ref="codeDiffEditor" class="diffEditor" :old-value="diffOldValue" :new-value="diffNewValue" />
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
      diffOldValue: '',
      diffNewValue: '',

      editorText: '',
      rightEditorText: '',

      isDiff: false,
      bOnlyShowDiff: false,
      bOneEditor: false
    }
  },

  methods: {
    changeEditorNum() {
      this.bOneEditor = !this.bOneEditor
      // if (this.bOneEditor) {
      //   this.$refs.leftEditor.init()
      // } else {
      //   this.$refs.leftEditor.init()
      //   this.$refs.rightEditor.init()
      // }
    },

    unique(arr) {
      return Array.from(new Set(arr))
    },
    uniqText(text) {
      var lines = text.split('\n')
      lines = this.unique(lines)
      var content = lines.join('\n')
      console.log(content)
      return content
    },
    contentUnique() {
      var text = this.uniqText(this.editorText.trim())
      this.$refs.leftEditor.setText(text)
      text = this.uniqText(this.rightEditorText.trim())
      this.$refs.rightEditor.setText(text)
    },
    sortText(text) {
      var lines = text.split('\n')
      lines.sort()
      var content = lines.join('\n')
      console.log(content)
      return content
    },
    contentSort() {
      var text = this.sortText(this.editorText.trim())
      this.$refs.leftEditor.setText(text)
      text = this.sortText(this.rightEditorText.trim())
      this.$refs.rightEditor.setText(text)
    },
    contentDiff() {
      this.isDiff = true
      console.log(this.$refs.codeDiffEditor)
      // this.$refs.codeDiffEditor.doDiff(this.editorText, this.rightEditorText)
    },
    onlyShowDiff() {
      if (this.bOnlyShowDiff) {
        this.$refs.codeDiffEditor.showAll()
      } else {
        this.$refs.codeDiffEditor.onlyShowDiff()
      }
      this.bOnlyShowDiff = !this.bOnlyShowDiff
    },
    contentEdit() {
      this.isDiff = false
    },
    leftContentChannge(text) {
      this.editorText = text
      this.diffOldValue = text
    },
    rightContentChannge(text) {
      this.rightEditorText = text
      this.diffNewValue = text
    }
  }
}
</script>

<style scoped>
.leftEditor {
  height: 800px;
  border:1px solid #cccccc;
  display:inline-block;
}

.rightEditor {
  height: 800px;
  border:1px solid #cccccc;
  display:inline-block;
}

.half-width {
width: 50%;
}

.full-width {
width: 100%;
}

.no-width {
width: 0%;
}

.diffEditor {
height: 800px;
 border:1px solid #cccccc;
}
</style>
