<template>
  <div class="detail">
    <el-form ref="postForm" :model="postForm" :rules="rules" class="form-container">
      <sticky :z-index="10" :class-name = "'sub-navbar postForm.status'">
      <el-button v-if="!isEdit" @click.prevent.stop="showGuide">显示帮助</el-button>
      <el-button v-loading="loading" style="margin-left:10px" type="success" @click="submitForm">
      {{isEdit?'编辑电子书':'新增电子书'}}</el-button>
      </sticky>
      <div class="detail-container">
      <el-row>
        <warning />
        <el-col :row="24">
          <ebook-upload></ebook-upload>
        </el-col>
        <el-col :row="24">
        </el-col>
      </el-row>
      </div>
    </el-form>
  </div>
</template>
<script>
import Sticky from '../../../components/Sticky/index'
import Warning from './warning'
import EbookUpload from '../../../components/EbookUpload/index'
export default {
  name: 'Detail',
  props:{
    isEdit:Boolean
  },
  data(){
    return {
      loading:false,
      postForm:{
        status:'deleted'
      }
    }
  },
  components:{
    Sticky,
    Warning,
    EbookUpload
  },
  methods:{
    submitForm(){
      this.loading = true
      setTimeout(() => {
        this.loading = false
      },1000)
    }
  }
}
</script>
<style lang="scss" scope>  
  @import "~@/styles/mixin.scss";
  .detail{
    position: relative;
    .detail-container {
      padding: 40px 45px 20px 50px;
      .preview-img {
        width: 200px;
        height: 270px;
      }
      .contents-wrapper {
        padding: 5px 0;
      }
    }
  }
</style>
