<template>
  <div>
    <div style="margin: 10px">
      <!-- <el-button type="info" @click="changeEditorNum">{{ bOneEditor ? '双编辑器' : '单编辑器' }}</el-button> -->
      <el-button type="info" size="small" @click="wordWrap">换行</el-button>
      <el-button type="info" size="small" @click="contentDelBlankLine">去空行</el-button>
      <el-button type="primary" size="small" @click="contentTrim">Trim</el-button>
      <el-button type="success" size="small" @click="contentSort">SORT</el-button>
      <el-button type="success" size="small" @click="contentUnique">UNIQ</el-button>
      <el-button type="info" size="small" @click="contentInter">交集</el-button>
      <el-button type="info" size="small" @click="contentSwap">交换</el-button>
      <el-button type="primary" size="small" @click="showFilter">过滤</el-button>
      <el-button type="primary" size="small" @click="showWordReplace">替换</el-button>
      <el-button type="success" size="small" @click="contentEdit">返回编辑</el-button>
      <el-button type="info" size="small" @click="contentDiff">对比</el-button>
      <el-button type="success" size="small" @click="onlyShowDiff">{{
        bOnlyShowDiff ? "查看全部" : "只看差异"
      }}</el-button>
      <el-button type="info" size="small" @click="urlDecode">URL解码</el-button>
      <el-button type="primary" size="small" @click="urlEncode">URL编码</el-button>
      <el-button type="success" size="small" @click="toUpper">转大写</el-button>
      <el-button type="info" size="small" @click="toLower">转小写</el-button>
    </div>

    <div v-show="bFilterMode" class="margin10 border1">
      <div class="margin10">
        <el-input
          v-model="filterPattern"
          style="width: 50%"
          placeholder="请输入过滤的正则表达式"
        />
        <el-button type="primary" size="small" class="margin10" @click="contentFilter"
          >确定</el-button
        >
      </div>
      <div class="margin10">
        <el-tag type="success">常用正则:</el-tag>

        <el-button
          v-for="item in regPatterns"
          :key="item.label"
          class="margin5"
          :data-pattern="item.pattern"
          :type="item.type"
          size="mini"
          @click="chooseFilterPattern(item.pattern)"
          >{{ item.label }}</el-button
        >
      </div>
    </div>

    <div v-show="bWordReplaceMode" class="margin10 border1">
      <div class="margin10">
        <el-input
          v-model="wordReplaceOld"
          style="width: 20%"
          placeholder="请输入要替换的旧字符串"
        />
        <span style="margin: 10px">替换为</span>
        <el-input
          v-model="wordReplaceNew"
          style="width: 20%"
          placeholder="请输入要替换的新字符串"
        />
        <el-button type="primary" size="small" class="margin10" @click="doReplace"
          >替换</el-button
        >
        <!-- <el-button
          type="primary"
          size="small"
          @click="doWordReplace"
        >单词替换</el-button> -->

      </div>

      <div class="margin10">
        <el-tag type="success">旧字符串:</el-tag>

        <el-button
          v-for="item in wordReplacePatterns"
          :key="item.label"
          class="margin5"
          :data-pattern="item.pattern"
          :type="item.type"
          size="mini"
          @click="chooseOldWordReplacePattern(item.pattern)"
          >{{ item.label }}</el-button
        >

        <el-tag  class="margin-left-100" type="success">新字符串:</el-tag>

        <el-button
          v-for="item in wordReplacePatterns"
          :key="item.label"
          class="margin5"
          :data-pattern="item.pattern"
          :type="item.type"
          size="mini"
          @click="chooseNewWordReplacePattern(item.pattern)"
          >{{ item.label }}</el-button
        >
      </div>

    </div>

    <div v-show="isDiff == false">
      <CodeEditor
        ref="leftEditor"
        class="leftEditor"
        :class="{ 'full-width': bOneEditor, 'half-width': !bOneEditor }"
        :text="editorText"
        @contentChannge="leftContentChannge"
      />
      <CodeEditor
        v-show="!bOneEditor"
        ref="rightEditor"
        class="rightEditor"
        :class="{ 'no-width': bOneEditor, 'half-width': !bOneEditor }"
        :text="rightEditorText"
        @contentChannge="rightContentChannge"
      />
    </div>

    <!-- <div v-show="isDiff" style="padding:30px;"> -->
    <div v-show="!bOneEditor">
      <CodeDiffEditor
        ref="codeDiffEditor"
        class="diffEditor"
        :old-value="diffOldValue"
        :new-value="diffNewValue"
      />
    </div>
  </div>
</template>

<script>
import CodeDiffEditor from "@/components/CodeDiffEditor";
import CodeEditor from "@/components/CodeEditor";
export default {
  components: {
    CodeDiffEditor,
    CodeEditor,
  },

  data() {
    return {
      regPatterns: [
        { type: "success", label: "IP", pattern: "\\d+\\.\\d+\\.\\d+\\.\\d+" },
        { type: "info", label: "数字", pattern: "\\d+" },
        {
          type: "danger",
          label: "IP:端口",
          pattern: "\\d+\\.\\d+\\.\\d+\\.\\d+:\\d+",
        },
        { type: "warning", label: "变量名", pattern: "[a-z_A-Z0-9]+" },
        { type: "primary", label: "模块", pattern: "mm[a-z_A-Z0-9]+" },
      ],
      wordReplacePatterns: [
      { type: "success", label: "换行", pattern: "\\n" },
        { type: "info", label: "','", pattern: "','" },
        { type: "info", label: ",", pattern: "," },

      ],
      wordReplaceOld: "\\n",
      wordReplaceNew: "",
      diffOldValue: "",
      diffNewValue: "",

      editorText: "",
      rightEditorText: "",

      isDiff: false,
      bOnlyShowDiff: false,
      bOneEditor: false,
      bFilterMode: false,
      bWordReplaceMode: false,
      filterPattern: "",
      isWordWrap: false,
    };
  },

  methods: {
    changeEditorNum() {
      this.bOneEditor = !this.bOneEditor;
      // if (this.bOneEditor) {
      //   this.$refs.leftEditor.init()
      // } else {
      //   this.$refs.leftEditor.init()
      //   this.$refs.rightEditor.init()
      // }
    },

    text2Lines(text) {
      var lines = text.split("\n");
      var newLines = [];
      lines.forEach((element) => {
        newLines.push(element.replace(/\r$/, ""));
      });
      return newLines;
    },

    unique(arr) {
      return Array.from(new Set(arr));
    },
    uniqText(text) {
      var lines = this.text2Lines(text);
      lines = this.unique(lines);
      var content = lines.join("\n");
      console.log(content);
      return content;
    },
    contentUnique() {
      var text = this.uniqText(this.editorText);
      this.$refs.leftEditor.setText(text);
      text = this.uniqText(this.rightEditorText);
      this.$refs.rightEditor.setText(text);
    },
    trimLines(text) {
      var lines = this.text2Lines(text);
      var newLines = [];
      lines.forEach((element) => {
        newLines.push(element.trim());
      });
      return newLines.join("\n");
    },
    contentTrim() {
      var text = this.trimLines(this.editorText);
      this.$refs.leftEditor.setText(text);
      text = this.trimLines(this.rightEditorText);
      this.$refs.rightEditor.setText(text);
    },
    sortText(text) {
      var lines = this.text2Lines(text);
      lines.sort();
      var content = lines.join("\n");
      console.log(content);
      return content;
    },
    contentSort() {
      var text = this.sortText(this.editorText);
      this.$refs.leftEditor.setText(text);
      text = this.sortText(this.rightEditorText);
      this.$refs.rightEditor.setText(text);
    },
    delBlankLine(text) {
      var lines = this.text2Lines(text);
      var newLines = [];
      lines.forEach((element) => {
        if (element.trim() === "") {
          return;
        }
        newLines.push(element);
      });
      return newLines.join("\n");
    },
    contentDelBlankLine() {
      var text = this.delBlankLine(this.editorText);
      this.$refs.leftEditor.setText(text);
      text = this.delBlankLine(this.rightEditorText);
      this.$refs.rightEditor.setText(text);
    },
    intersect(text1, text2) {
      var lines1 = this.text2Lines(text1);
      var lines2 = this.text2Lines(text2);
      var comm = [];
      var inter1 = [];
      var inter2 = [];
      lines1.forEach((el) => {
        if (lines2.indexOf(el) !== -1) {
          comm.push(el);
        }
      });
      lines1.forEach((el) => {
        if (comm.indexOf(el) === -1) {
          inter1.push(el);
        }
      });
      lines2.forEach((el) => {
        if (comm.indexOf(el) === -1) {
          inter2.push(el);
        }
      });
      return [comm, inter1, inter2];
    },
    contentInter() {
      var text1 = this.delBlankLine(this.editorText);
      var text2 = this.delBlankLine(this.rightEditorText);
      var res = this.intersect(text1, text2);
      console.log(res);
      this.$refs.leftEditor.setText(res[0].concat(res[1]).join("\n"));
      this.$refs.rightEditor.setText(res[0].concat(res[2]).join("\n"));

      this.contentDiff();
    },
    contentSwap() {
      var text = this.editorText;
      this.$refs.leftEditor.setText(this.rightEditorText);
      this.$refs.rightEditor.setText(text);
    },  
    showFilter() {
      this.bFilterMode = !this.bFilterMode;
    },
    showWordReplace() {
      this.bWordReplaceMode = !this.bWordReplaceMode;
    },
    contentFilter() {
      var re = new RegExp(this.filterPattern, "g");
      var res = this.editorText.matchAll(re);
      var result = [...res];
      var lines = [];
      for (var i in result) {
        lines.push(result[i][0]);
      }
      this.$refs.rightEditor.setText(lines.join("\n"));
    },
    doWordReplace() {
      const oldText = this.wordReplaceOld;
      const newText = this.wordReplaceNew;
      
      // Improved regex with word boundaries and escape special characters
      const escapedText = oldText.replace(/[-\/\\^$*+?.()|[\]{}]/g, '\\$&');
      const regex = new RegExp(`(^|\\s)${escapedText}(?=\\s|$)`, 'g');
      
      // Replace text in left editor and update right editor
      const replacedText = this.editorText.replace(regex, `$1${newText}`);
      this.$refs.rightEditor.setText(replacedText);
    },
    doReplace() {
      const oldText = this.wordReplaceOld;
      const newText = this.wordReplaceNew;
      const regex = new RegExp(oldText, 'g');
      // Replace text in left editor and update right editor
      const replacedText = this.editorText.replace(regex, newText);
      this.$refs.rightEditor.setText(replacedText);
    },
    chooseFilterPattern(pattern) {
      console.log(pattern);
      this.filterPattern = pattern;
    },
    chooseOldWordReplacePattern(pattern) {
      console.log(pattern);
      this.wordReplaceOld = pattern;
    },
    chooseNewWordReplacePattern(pattern) {
      console.log(pattern);
      this.wordReplaceNew = pattern;
    },  

    contentDiff() {
      this.isDiff = true;
      console.log(this.$refs.codeDiffEditor);
      // this.$refs.codeDiffEditor.doDiff(this.editorText, this.rightEditorText)
    },
    onlyShowDiff() {
      if (this.bOnlyShowDiff) {
        this.$refs.codeDiffEditor.showAll();
      } else {
        this.$refs.codeDiffEditor.onlyShowDiff();
      }
      this.bOnlyShowDiff = !this.bOnlyShowDiff;
    },
    contentEdit() {
      this.isDiff = false;
    },
    leftContentChannge(text) {
      this.editorText = text;
      this.diffOldValue = text;
    },
    rightContentChannge(text) {
      this.rightEditorText = text;
      this.diffNewValue = text;
    },
    urlDecode() {
      this.$refs.rightEditor.setText(decodeURIComponent(this.editorText));
    },
    urlEncode() {
      this.$refs.rightEditor.setText(encodeURIComponent(this.editorText));
    },
    toUpper() {
      this.$refs.rightEditor.setText(this.editorText.toUpperCase());
    },
    toLower() {
      this.$refs.rightEditor.setText(this.editorText.toLowerCase());
    },
    wordWrap() {
      this.isWordWrap = !this.isWordWrap;
      var onOption = { wordWrap: "on" };
      var offOption = { wordWrap: "off" };
      if (this.isWordWrap) {
        this.$refs.rightEditor.updateOptions(onOption);
        this.$refs.leftEditor.updateOptions(onOption);
        this.$refs.codeDiffEditor.updateOptions(onOption);
      } else {
        this.$refs.rightEditor.updateOptions(offOption);
        this.$refs.leftEditor.updateOptions(offOption);
        this.$refs.codeDiffEditor.updateOptions(offOption);
      }
    },
  },
};
</script>

<style scoped>
.leftEditor {
  height: 800px;
  border: 1px solid #cccccc;
  display: inline-block;
}

.rightEditor {
  height: 800px;
  border: 1px solid #cccccc;
  display: inline-block;
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
  border: 1px solid #cccccc;
}

.margin10 {
  margin: 10px;
}

.margin-left-100 {
  margin: 0px 0px 0px 100px;
}

.margin5 {
  margin: 5px;
}

.border1 {
  border: 1px solid #cccccc;
}
</style>
