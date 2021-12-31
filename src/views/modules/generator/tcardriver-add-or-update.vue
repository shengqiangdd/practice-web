<template>
  <el-dialog
    :title="!dataForm.id ? '司机信息添加' : '司机信息修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    :append-to-body="true"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="80px"
    >
      <el-form-item label="姓名" prop="name">
        <el-input v-model="dataForm.name" placeholder="姓名"></el-input>
      </el-form-item>
      <el-form-item label="性别" prop="sex">
        <el-input v-model="dataForm.sex" placeholder="性别"></el-input>
      </el-form-item>
      <el-form-item label="手机" prop="tel">
        <el-input v-model="dataForm.tel" placeholder="手机"></el-input>
      </el-form-item>
      <el-form-item label="准驾车型" prop="type">
        <el-input v-model="dataForm.type" placeholder="准驾车型"></el-input>
      </el-form-item>
      <el-form-item label="备注" prop="remark">
        <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="closeDialog">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{ dataForm.id ? '保存' : '确定' }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
export default {
  data() {
    return {
      visible: false,
      addId: 0,
      dataForm: {
        id: 0,
        name: "",
        sex: "",
        tel: "",
        type: "",
        remark: "",
      },
      dataRule: {
        name: [{ required: true, message: "姓名不能为空", trigger: "blur" }],
        sex: [{ required: true, message: "性别不能为空", trigger: "blur" }],
        tel: [{ required: true, message: "手机不能为空", trigger: "blur" }],
        type: [
          { required: true, message: "准驾车型不能为空", trigger: "blur" },
        ],
        remark: [{ required: true, message: "备注不能为空", trigger: "blur" }],
      },
    };
  },
  methods: {
    init(id, isAdd) {
      this.dataForm.id = id || 0;
      this.visible = true;
      if (isAdd) {
        this.addId = this.dataForm.id;
      } else {
        this.$nextTick(() => {
          this.$refs["dataForm"].resetFields();
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(
                `/generator/tcardriver/info/${this.dataForm.id}`
              ),
              method: "get",
              params: this.$http.adornParams(),
            }).then(({ data }) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.tCarDriver.name;
                this.dataForm.sex = data.tCarDriver.sex;
                this.dataForm.tel = data.tCarDriver.tel;
                this.dataForm.type = data.tCarDriver.type;
                this.dataForm.remark = data.tCarDriver.remark;
              }
            });
          }
        });
      }
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/generator/tcardriver/${this.addId > 0 ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              name: this.dataForm.name,
              sex: this.dataForm.sex,
              tel: this.dataForm.tel,
              type: this.dataForm.type,
              remark: this.dataForm.remark,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.addId = 0
                  this.$refs["dataForm"].resetFields();
                  this.visible = false;
                  this.$emit("refreshDataList");
                  this.$emit('closeDialog')
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    // 取消选择
    closeDialog() {
      this.$refs["dataForm"].resetFields();
      this.visible = false
      this.$emit('closeDialog')
    }
  },
};
</script>
