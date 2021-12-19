<template>
  <div ref="diffContainer" class="the-code-diff-editor-container" />
</template>

<script>
import * as monaco from 'monaco-editor/esm/vs/editor/editor.api'
import 'monaco-editor/esm/vs/basic-languages/shell/shell.contribution'
import 'monaco-editor/esm/vs/basic-languages/sql/sql.contribution'
import 'monaco-editor/esm/vs/basic-languages/ini/ini.contribution'
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
      this.oldModel.setValue(this.oldValue)
    },
    newValue: function() {
      this.newModel.setValue(this.newValue)
    }
  },
  mounted() {
    this.init()
  },

  methods: {
    init() {
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
    },

    doDiff(oldText, newText) {
      this.oldValue = oldText
      this.newValue = newText
      // this.oldModel.setValue(oldText)
      // this.newModel.setValue(newText)
    },

    onlyShowDiff() {
      var diffEditor = this.monacoDiffInstance
      var versionContentLines = this.oldValue.split('\n').length
      var onlineContentLines = this.newValue.split('\n').length
      var lineChanges = diffEditor.getLineChanges().slice()
      console.log('diffEditor.getLineChanges().slice()')
      console.log(lineChanges)
      var originHideRanges = []; var modifyHideRanges = []
      for (var i in lineChanges) {
        if (lineChanges[i].originalEndLineNumber === 0) {
          lineChanges[i].originalEndLineNumber = versionContentLines + 1
        }
        if (lineChanges[i].modifiedEndLineNumber === 0) {
          lineChanges[i].modifiedEndLineNumber = onlineContentLines + 1
        }
      }
      lineChanges.unshift({ 'originalStartLineNumber': 0, 'originalEndLineNumber': 0, 'modifiedStartLineNumber': 0, 'modifiedEndLineNumber': 0 })
      lineChanges.push({ 'originalStartLineNumber': versionContentLines + 1, 'originalEndLineNumber': versionContentLines + 1, 'modifiedStartLineNumber': onlineContentLines + 1, 'modifiedEndLineNumber': onlineContentLines + 1 })
      console.log('calc lineChanges')
      console.log(lineChanges)
      for (var i = 0; i < lineChanges.length - 1; i++) {
        if (lineChanges[i].originalEndLineNumber + 1 <= lineChanges[i + 1].originalStartLineNumber - 1) {
          originHideRanges.push(new monaco.Range(lineChanges[i].originalEndLineNumber + 1, 1, lineChanges[i + 1].originalStartLineNumber - 1, 1))
        }
        if (lineChanges[i].modifiedEndLineNumber + 1 <= lineChanges[i + 1].modifiedStartLineNumber - 1) {
          modifyHideRanges.push(new monaco.Range(lineChanges[i].modifiedEndLineNumber + 1, 1, lineChanges[i + 1].modifiedStartLineNumber - 1, 1))
        }
      }
      console.log(originHideRanges)
      console.log(modifyHideRanges)
      diffEditor.getOriginalEditor().setHiddenAreas(originHideRanges)
      diffEditor.getModifiedEditor().setHiddenAreas(modifyHideRanges)
    },
    showAll() {
      var diffEditor = this.monacoDiffInstance
      diffEditor.getOriginalEditor().setHiddenAreas([])
      diffEditor.getModifiedEditor().setHiddenAreas([])
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
