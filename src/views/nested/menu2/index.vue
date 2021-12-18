<template>
  <div style="padding:30px;">
    <el-alert :closable="false" title="menu 2" />
    <CodeDiffEditor class="editor" :old-value="old" :value="val" />
  </div>
</template>

<script>
import CodeDiffEditor from '../../../components/CodeDiffEditor'
export default {
  components: {
    CodeDiffEditor
  },
  data() {
    return {
      old: "{'hello':'world'}",
      val: "{'hello':'world', 'hello2':'yoxi'}"
    }
  }
}
</script>

<style scoped>
.editor{
  height: 500px;
}
</style>
