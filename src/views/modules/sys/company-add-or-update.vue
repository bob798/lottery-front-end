<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="" prop="name">
      <el-input v-model="dataForm.name" placeholder="名称"></el-input>
    </el-form-item>
    <el-form-item label="" prop="personTotal">
      <el-input v-model="dataForm.personTotal" placeholder="人员总数"></el-input>
    </el-form-item>
    <el-form-item label="" prop="qrcodeUrl">
      <el-input v-model="dataForm.qrcodeUrl" placeholder="二维码路径"></el-input>
    </el-form-item>
    <el-form-item label="" prop="backgroudImgUrl">
      <el-input v-model="dataForm.backgroudImgUrl" placeholder="背景图路径"></el-input>
    </el-form-item>
    <el-form-item label="" prop="endTime">
      <el-input v-model="dataForm.endTime" placeholder="结束时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          personTotal: '',
          qrcodeUrl: '',
          backgroudImgUrl: '',
          endTime: '',
          createTime: '',
          utime: ''
        },
        dataRule: {
          name: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          personTotal: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          qrcodeUrl: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          backgroudImgUrl: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          endTime: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          utime: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/company/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.company.name
                this.dataForm.personTotal = data.company.personTotal
                this.dataForm.qrcodeUrl = data.company.qrcodeUrl
                this.dataForm.backgroudImgUrl = data.company.backgroudImgUrl
                this.dataForm.endTime = data.company.endTime
                this.dataForm.createTime = data.company.createTime
                this.dataForm.utime = data.company.utime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/company/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'personTotal': this.dataForm.personTotal,
                'qrcodeUrl': this.dataForm.qrcodeUrl,
                'backgroudImgUrl': this.dataForm.backgroudImgUrl,
                'endTime': this.dataForm.endTime,
                'createTime': this.dataForm.createTime,
                'utime': this.dataForm.utime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
