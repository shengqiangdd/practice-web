<template>
  <el-dialog
    :title="!dataForm.deptid ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="父部门id" prop="parentId">
      <el-input v-model="dataForm.parentId" placeholder="父部门id"></el-input>
    </el-form-item>
    <el-form-item label="部门名称" prop="deptName">
      <el-input v-model="dataForm.deptName" placeholder="部门名称"></el-input>
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
          deptid: 0,
          parentId: '',
          deptName: '',
          ordernum: ''
        },
        dataRule: {
          parentId: [
            { required: true, message: '父部门id不能为空', trigger: 'blur' }
          ],
          deptName: [
            { required: true, message: '部门名称不能为空', trigger: 'blur' }
          ],
          ordernum: [
            { required: true, message: '显示顺序不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.deptid = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.deptid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/tcardept/info/${this.dataForm.deptid}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.parentId = data.tCarDept.parentId
                this.dataForm.deptName = data.tCarDept.deptName
                this.dataForm.ordernum = data.tCarDept.ordernum
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
              url: this.$http.adornUrl(`/generator/tcardept/${!this.dataForm.deptid ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'deptid': this.dataForm.deptid || undefined,
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
