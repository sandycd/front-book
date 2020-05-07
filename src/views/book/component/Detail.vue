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
            <ebook-upload
              v-model="postForm.image_url"
              :file-list = "fileList"
              :disabled ="isEdit"
              @onSuccess="onUploadSuccess"
              @onRemove="onUploadRemove"
            ></ebook-upload>
          </el-col>
          <el-col :row="24">
            <el-form-item style="margin-bottom:24px" prop="title">
              <MDinput v-model="postForm.title" :maxlength="100" name="name" required>书名</MDinput>
            </el-form-item>
            <div>
              <el-row>
                <el-col :span="12" class="form-item-author">
                  <el-form-item :label-width="labelWidth" label="作者:">
                    <el-input
                      v-model="postForm.author"
                      placeholder="作者"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label-width="labelWidth" label="出版社:">
                    <el-input
                      v-model="postForm.publisher"
                      placeholder="出版社"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label-width="labelWidth" label="语言:">
                    <el-input
                      v-model="postForm.language"
                      placeholder="语言"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label-width="labelWidth" label="根文件:">
                    <el-input
                      v-model="postForm.rootFile"
                      placeholder="根文件"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label-width="labelWidth" label="文件路径:">
                    <el-input
                      v-model="postForm.filePath"
                      placeholder="文件路径"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label-width="labelWidth" label="解压路径:">
                    <el-input
                      v-model="postForm.unzipPath"
                      placeholder="解压路径"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label-width="labelWidth" label="封面路径:">
                    <el-input
                      v-model="postForm.coverPath"
                      placeholder="封面路径"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item :label-width="labelWidth" label="文件名称:">
                    <el-input
                      v-model="postForm.fileName"
                      placeholder="文件名称"
                      style="width:100%"
                    ></el-input>
                  </el-form-item>
                </el-col>

              </el-row>
              <el-row>
              <el-col :span="24">
              <el-form-item label-width="60px" label="封面">
                <a v-if="postForm.cover" :href="postForm.href" target="_blank">
                  <img :src="postForm.cover" class="preview-img">
                </a>
                <span v-else>无</span>
              </el-form-item>
              </el-col>
              </el-row>
            </div>
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
  components: {
    Sticky,
    Warning,
    EbookUpload
  },
  props: {
    isEdit: Boolean
  },
  data() {
    return {
      loading: false,
      postForm: {
        status: 'deleted'
      }
    }
  },

  methods: {
    submitForm() {
      this.loading = true
      setTimeout(() => {
        this.loading = false
      }, 1000)
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
