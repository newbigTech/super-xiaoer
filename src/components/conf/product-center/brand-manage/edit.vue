<template>
  <div class="content">
    <break-crumb :titles="titles"></break-crumb>
    <section>
      <el-col :span="24" class="mod-toolbar">
        <el-form :rules="rules" ref="ruleForm" :model="formEntity" label-width="120px" label-position="left"
                 class="form">
          <el-form-item label="品牌名称" prop="brandName" required>
            <el-col :span="6">
              <el-input v-model="formEntity.brandName" placeholder="请输入品牌名称"></el-input>
            </el-col>
          </el-form-item>
          <el-form-item label="品牌LOGO" prop="imageKey"
                        :rules="rules.imageKey">
            <div class="custom-uploader">
              <div class="el-upload" @click="openUploadDialog()">
                <img v-if="formEntity.imageUrl" :src="formEntity.imageUrl" class="uploader-img">
                <i v-else class="el-icon-plus custom-uploader-icon"></i>
              </div>
            </div>
            <p class="custom-uploader-tips">品牌LOGO尺寸要求宽度为120像素，高度为120像素、比例为3:1的图片；支持格式gif,jpg,png</p>
          </el-form-item>
          <el-form-item label-width="120px">
            <el-button type="primary" class="submit-btn" @click="update('ruleForm')">提交</el-button>
            <el-button @click="goBack">返回</el-button>
          </el-form-item>
        </el-form>
      </el-col>
    </section>
    <el-dialog
      title="提示"
      :visible.sync="successDialogVisible"
      size="tiny">
      <span>修改成功!</span>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="goBack" class="confirm-btn">确 定</el-button>
      </span>
    </el-dialog>
    <upload-file-dialog @watchDialog="closeUploadDialog" @passSelectedPicUrl="getSelectedPicuUrl"
                        :isShow="isShowUploadDialog"></upload-file-dialog>
  </div>
</template>
<script>
  import Util from '@/assets/js/util'
  import breakCrumb from '@/components/common/breakCrumb'
  import uploadFileDialog from '@/components/common/packageDialog/uploadPicDialog'
  export default {
    components: {
      breakCrumb,
      uploadFileDialog
    },
    data(){
      return {
        isShowUploadDialog: false,
        successDialogVisible: false,
        titles: [{id: 1, name: '商品中心'}, {id: 2, name: '品牌管理'}, {id: 3, name: '品牌编辑'}],
        formEntity: {
          brandId: '',
          brandName: '',
          imageKey: '',
          imageUrl: ''
        },
        ruleForm: {
          brandName: ''
        },
        rules: {
          brandName: [
            {validator: Util.validateForm.inputValidateCommon}
          ]
          // imageKey: [
          //   {required: true, message: '请上传品牌LOGO'}
          // ]
        }
      }
    },
    methods: {
      /**
       * 获取品牌信息
       * @param id 品牌Id
       */
      getBrandDetail: function (id) {
        let url = this.$apiUrl.getBrandDetailUrl
        let param = {
          brandId: id
        }
        if(id) {
          this.$ajax.post(url, param).then((res) => {
            this.formEntity.brandId = id
            this.formEntity.brandName = res.data.brandName
            this.formEntity.imageKey = res.data.imageKey
            this.formEntity.imageUrl = res.data.imageUrl
          })
        }
      },
      /**
       * 更新品牌信息
       * @param formName 表单名
       */
      update: function (formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            let url = this.$apiUrl.editBrandUrl
            let param = Object.assign({}, this.formEntity)
            this.$ajax.post(url, param).then((res) => {
              if(!res.error) {
                this.successDialogVisible = true
              }
            })
          } else {
            return false
          }
        })
      },
      /**
       * 返回品牌管理页
       */
      goBack: function () {
        this.successDialogVisible = false
        this.$router.push({name: '品牌列表', params: {currentPage: this.$route.query.currentPage}})
      },
      /**
       * 打开上传相册
       */
      openUploadDialog() {
        this.isShowUploadDialog = true
      },
      /**
       * 关闭图片上传对话框
       */
      closeUploadDialog: function () {
        this.isShowUploadDialog = false
      },
      /**
       * 获取相册分类中选中的图片
       */
      getSelectedPicuUrl: function (result) {
        this.isShowUploadDialog = false
        this.formEntity.imageKey = result[0].imageKey
        this.formEntity.imageUrl = result[0].imageUrl
      }
    },
    mounted(){
      // 获取品牌详情
      this.getBrandDetail(this.$route.query.brandId)
    }
  }
</script>
<style scoped>
  .mod-toolbar {
    background-color: #ffffff;
    padding: 30px 30px 40px;
  }
  .form > .el-form-item {
    margin-bottom: 20px;
  }
  .submit-btn {
    background-color: #33bbab;
    border: none;
  }
  .line {
    text-align: center;
    margin-left: 10px;
    line-height: 36px;
    color: #ccc;
  }
</style>
<style>
  .custom-uploader .el-upload {
    border: 1px dashed #8c939d;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .custom-uploader .el-upload:hover {
    border-color: #20a0ff;
  }
  .custom-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .custom-uploader-tips {
    color: #999;
  }
  .uploader-img {
    width: 178px;
    height: 178px;
    display: block;
  }
</style>
