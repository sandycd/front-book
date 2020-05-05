<template>
  <div class="singleImageUpload2 upload-container">
    <el-upload>
      <i class="el-icon-upload" />
      <div v-if="fileList.length === 0" class="el-upload__text">
        请将电子书拖入或
        <em>点击上传</em>
      </div>
      <div v-else class="el-upload__text">
        图书已上传
      </div>
      </el-upload>
    
  </div>
</template>
<script>
export default {
  data(){
    return {
      fileList:[]
    }
  }
}
</script>
<style lang="scss">
  
</style>
