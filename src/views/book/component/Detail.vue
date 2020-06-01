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
              <el-row>
                <el-col :span="24">
                  <el-form-item label-width="60px" label="目录">
                    <div
                    v-if="postForm.content&&postForm.content.length>0" class="contents-wrapper">
                    <el-tree :data="contentsTree" @node-click="onContentClick"></el-tree>
                    </div>
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
import MDinput from '@/components/MDinput'
import {validURL} from '@/utils/validate'
import {createBook,updateBook,getBook} from '@/api/book'
import Sticky from '../../../components/Sticky/index'
import Warning from './warning'
import EbookUpload from '../../../components/EbookUpload/index'
import steps from './steps'
import Driver from 'driver.js' // import driver.js
import 'driver.js/dist/driver.min.css' // import driver.js css

const defaultForm = {
      title: '', // 书名
      author: '', // 作者
      publisher: '', // 出版社
      language: '', // 语种
      rootFile: '', // 根文件路径
      cover: '', // 封面图片URL
      coverPath: '', // 封面图片路径
      fileName: '', // 文件名
      originalName: '', // 文件原始名称
      filePath: '', // 文件所在路径
      unzipPath: '', // 解压文件所在路径
      contents: [] // 目录
}
export default {
  name: 'Detail',
  components: {
    Sticky,
    Warning,
    EbookUpload,
    MDinput
  },
    props: {
      isEdit: {
        type: Boolean,
        default: false
      }
    },
  data() {
    const validateRequire= (rule, value, callback) =>{
      if(value == ''){
        this.$message({
          message:rule.field +'为必传项',
          type:'error'
        })
        callback(new Error(rule.field + '为必传项'))
      }else{
        callback()
      }
    }
    const validateSourceUri = (rule, value, callback) =>{
      if(value){
        if(validURL(value)){
          callback()
        }else{
          this.$message({
            message:'外链url填写不正确',
            type:'error'
          })
          callback(new Error('外链url填写不正确'))
        }
      }else{
        callback()
      }
    }
    return {
      postForm: Object.assign({},defaultForm),
      loading: false,
      useListOptions: [],
      rules:{
        imgage_uri: [{validator:validateRequire}],
        title: [{validator:validateRequire}],
        content: [{validator:validateRequire}],
        source_uri: [{validator:validateSourceUri,trriger:"blur"}]
      },
      temRoute: {},
      fileList: [],
      contentsTree: [],
      labelWidth: '120px',
      driver:null
    }
  },
  created() {
    if(this.isEdit){
      const fileName = this.$route.params && this.$route.params.fileName
      this.getBookData(fileName)
    }
    this.tempRoute = Object.assign({},this.$route)
  },
  mounted() {
    this.driver = new Driver({
      nextBtnText: '下一个',
      prevBtnText: '上一个',
      closeBtnText: '关闭',
      doneBtnText: '完成'
    })
  },
  methods: {
  
    submitForm() {
      this.$refs.postForm.validate(valid=>{
        if(valid){
          this.loading = true;
          const book = Object.assign({},this.postForm)
          delete book.contents;
          if(!this.isEdit){
            createBook(book).then(respone=>{
              console.log('creakbook',respone);
              this.loading =false;
              this.$notify({
                title:'成功',
                message:respone.msg,
                type:'success',
                duration:2000
              })
              this.toDafault();

            }).catch(()=>{
              this.loading =false;
            })
          }else{
            updateBook(book).then(respone=>{
              console.log('updateBook',respone);
              this.loading = false;
              this.$notify({
                title: '成功',
                message: respone.msg,
                type: 'success',
                duration: 2000
              })
            }).catch(() => {
              this.loading = false;
            })
          }
        } else {
          return false
        }
      })
    },
    getBookData(fileName) {
      getBook(fileName).then(respone => {
        this.setData(respone.msg)
      })
    },

    onUploadSuccess(data) {
      this.setData(data)
    },
    setData(data) {
      const {
        title,
        author,
        publisher,
        language,
        rootFile,
        cover,
        originalName,
        url,
        contents,
        contentsTree,
        fileName,
        coverPath,
        filePath,
        unzipPath
      } = data
      this.postForm = {
        title,
        author,
        publisher,
        language,
        rootFile,
        cover,
        url,
        originalName,
        contents,
        fileName,
        coverPath,
        filePath,
        unzipPath
      }
      this.fileList = [{ name: originalName, url }]
      this.contentsTree = contentsTree
    },
    onUploadRemove() {
      this.toDefault()
    },
    toDefault() {
      this.postFrom = Object.assign({}, defaultForm)
      this.fileList = []
      this.contentsTree = []
    },
    onContentClick(data) {
      const { text } = data
      if (text) {
        window.open(text)
      }
    },
    showGuide() {
      this.driver.defineSteps(steps)
      this.driver.start()
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
