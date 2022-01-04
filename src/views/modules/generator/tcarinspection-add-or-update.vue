<template>
  <el-dialog
    :title="!dataForm.id ? '年检记录信息' : '年检更新信息'"
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
      <el-row>
        <el-col :span="10">
          <el-form-item label="车号" prop="carnum">
            <el-input v-model="dataForm.carnum" placeholder="车号"></el-input>
          </el-form-item>
          <el-form-item label="年检日期" prop="njdate">
            <el-date-picker
              v-model="dataForm.njdate"
              type="date"
              align="right"
              placeholder="年检日期"
              style="width: 100%"
              value-format="yyyy-MM-dd"
            >
            </el-date-picker>
          </el-form-item>
          <el-form-item label="车管所" prop="adminoffice">
            <el-input
              v-model="dataForm.adminoffice"
              placeholder="车管所"
            ></el-input>
          </el-form-item>
          <el-form-item label="备注" prop="remark">
            <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="年检号" prop="number">
            <el-input v-model="dataForm.number" placeholder="年检号"></el-input>
          </el-form-item>
          <el-form-item label="年检费用" prop="njcost">
            <el-input
              v-model="dataForm.njcost"
              placeholder="年检费用"
            ></el-input>
          </el-form-item>
          <el-form-item label="到期日期" prop="expiredate">
            <el-date-picker
              v-model="dataForm.expiredate"
              type="date"
              align="right"
              placeholder="到期日期"
              style="width: 100%"
              value-format="yyyy-MM-dd"
            >
            </el-date-picker>
          </el-form-item>
          <el-form-item label="经手人" prop="transactor">
            <el-input
              v-model="dataForm.transactor"
              placeholder="经手人"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="closeDialog">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{
        dataForm.id ? "保存" : "确定"
      }}</el-button>
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
        carid: "",
        number: "",
        njdate: "",
        njcost: "",
        adminoffice: "",
        expiredate: "",
        remark: "",
        transactor: "",
        carnum: "",
      },
      dataRule: {
        carid: [{ required: true, message: "车辆ID不能为空", trigger: "blur" }],
        number: [
          { required: true, message: "年检号不能为空", trigger: "blur" },
        ],
        njdate: [
          { required: true, message: "年检日期不能为空", trigger: "blur" },
        ],
        njcost: [
          { required: true, message: "年检费用不能为空", trigger: "blur" },
        ],
        adminoffice: [
          { required: true, message: "车管所不能为空", trigger: "blur" },
        ],
        expiredate: [
          { required: true, message: "到期日期不能为空", trigger: "blur" },
        ],
        remark: [{ required: true, message: "备注不能为空", trigger: "blur" }],
        transactor: [
          { required: true, message: "经手人不能为空", trigger: "blur" },
        ],
        carnum: [{ required: true, message: "车号不能为空", trigger: "blur" }],
      },
    };
  },
  methods: {
    init(id, isAdd, row) {
      this.dataForm.id = id || 0;
      this.visible = true;
      if (isAdd && row) {
        this.addId = this.dataForm.id;
        this.dataForm.carnum = row.number;
      } else {
        this.$nextTick(() => {
          this.$refs["dataForm"].resetFields();
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(
                `/generator/tcarinspection/info/${this.dataForm.id}`
              ),
              method: "get",
              params: this.$http.adornParams(),
            }).then(({ data }) => {
              if (data && data.code === 0) {
                this.dataForm.carid = data.tCarInspection.carid;
                this.dataForm.number = data.tCarInspection.number;
                this.dataForm.njdate = data.tCarInspection.njdate;
                this.dataForm.njcost = data.tCarInspection.njcost;
                this.dataForm.adminoffice = data.tCarInspection.adminoffice;
                this.dataForm.expiredate = data.tCarInspection.expiredate;
                this.dataForm.remark = data.tCarInspection.remark;
                this.dataForm.transactor = data.tCarInspection.transactor;
                this.dataForm.carnum = data.tCarInspection.carnum;
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
              `/generator/tcarinspection/${this.addId > 0 ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              carid: this.addId || undefined,
              carnum: this.dataForm.carnum,
              number: this.dataForm.number,
              njdate: this.dataForm.njdate,
              njcost: this.dataForm.njcost,
              adminoffice: this.dataForm.adminoffice,
              expiredate: this.dataForm.expiredate,
              remark: this.dataForm.remark,
              transactor: this.dataForm.transactor,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.addId = 0;
                  this.$refs["dataForm"].resetFields();
                  this.visible = false;
                  this.$emit("closeDialog");
                  this.$emit("refreshDataList");
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          }).catch(_=> {})
        }
      });
    },
    // 取消新增或修改
    closeDialog() {
      this.$refs["dataForm"].resetFields();
      this.visible = false;
      this.$emit("closeDialog");
    },
  },
};
</script>
<style scoped>
/deep/ .el-col {
  margin-left: 5%;
}
</style>