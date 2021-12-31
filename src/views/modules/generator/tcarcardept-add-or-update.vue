<template>
  <el-dialog
    :title="!dataForm.parentId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="父ID" prop="parentId">
      <el-input v-model="dataForm.parentId" placeholder="父ID"></el-input>
    </el-form-item>
    <el-form-item label="单位名称" prop="deptName">
      <el-input v-model="dataForm.deptName" placeholder="单位名称"></el-input>
    </el-form-item>
    <el-form-item label="显示顺序" prop="ordernum">
      <el-input v-model="dataForm.ordernum" placeholder="显示顺序"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{ dataForm.id ? '保存' : '确定' }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          usecompany: 0,
          parentId: '',
          deptName: '',
          ordernum: ''
        },
        dataRule: {
          parentId: [
            { required: true, message: '父ID不能为空', trigger: 'blur' }
          ],
          deptName: [
            { required: true, message: '单位名称不能为空', trigger: 'blur' }
          ],
          ordernum: [
            { required: true, message: '显示顺序不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.usecompany = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.usecompany) {
            this.$http({
              url: this.$http.adornUrl(`/generator/tcarcardept/info/${this.dataForm.usecompany}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.parentId = data.tCarCarDept.parentId
                this.dataForm.deptName = data.tCarCarDept.deptName
                this.dataForm.ordernum = data.tCarCarDept.ordernum
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
              url: this.$http.adornUrl(`/generator/tcarcardept/${!this.dataForm.usecompany ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'usecompany': this.dataForm.usecompany || undefined,
                'parentId': this.dataForm.parentId,
                'deptName': this.dataForm.deptName,
                'ordernum': this.dataForm.ordernum
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
