<template>
  <el-dialog
    :title="!dataForm.id ? '保险记录信息' : '保险更新信息'"
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
          <el-form-item label="车型" prop="carmodel">
            <el-input v-model="dataForm.carmodel" placeholder="车型"></el-input>
          </el-form-item>
          <el-form-item label="保险时间" prop="bxdate">
            <el-date-picker
              v-model="dataForm.bxdate"
              type="date"
              align="right"
              placeholder="保险时间"
              style="width: 100%"
              value-format="yyyy-MM-dd"
            >
            </el-date-picker>
          </el-form-item>
          <el-form-item label="保险费用" prop="cost">
            <el-input
              type="number"
              v-model.number="dataForm.cost"
              placeholder="保险费用"
            ></el-input>
          </el-form-item>
          <el-form-item label="保险公司" prop="company">
            <el-select v-model="dataForm.company" placeholder="">
              <el-option
                v-for="item in bxCompanys"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="经手人" prop="transactor">
            <el-input
              v-model="dataForm.transactor"
              placeholder="经手人"
            ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="保单号" prop="number">
            <el-input v-model="dataForm.number" placeholder="保单号"></el-input>
          </el-form-item>
          <el-form-item label="使用单位" prop="usecompany">
            <el-input
              v-model="dataForm.usecompany"
              placeholder="使用单位"
            ></el-input>
          </el-form-item>
          <el-form-item label="保险种类" prop="bxtype">
            <el-select v-model="dataForm.bxtype" placeholder="">
              <el-option
                v-for="item in bxTypes"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              >
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="保险金额" prop="amount">
            <el-input
              type="number"
              v-model.number="dataForm.amount"
              placeholder="保险金额"
            ></el-input>
          </el-form-item>
          <el-form-item label="到期时间" prop="expiredate">
            <el-date-picker
              v-model="dataForm.expiredate"
              type="date"
              align="right"
              placeholder="到期时间"
              style="width: 100%"
              value-format="yyyy-MM-dd"
            >
            </el-date-picker>
          </el-form-item>
          <el-form-item label="备注" prop="remark">
            <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
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
        bxdate: "",
        bxtype: "",
        cost: "",
        amount: "",
        company: "",
        transactor: "",
        expiredate: "",
        remark: "",
        usecompany: "",
        carmodel: "",
        carnum: "",
      },
      dataRule: {
        carid: [{ required: true, message: "车辆ID不能为空", trigger: "blur" }],
        number: [
          { required: true, message: "保单号不能为空", trigger: "blur" },
        ],
        bxdate: [
          { required: true, message: "保险时间不能为空", trigger: "blur" },
        ],
        bxtype: [{ required: true, message: "保险种类", trigger: "blur" }],
        cost: [
          { required: true, message: "保险费用不能为空", trigger: "blur" },
        ],
        amount: [
          { required: true, message: "保险金额不能为空", trigger: "blur" },
        ],
        company: [
          { required: true, message: "保险公司不能为空", trigger: "blur" },
        ],
        transactor: [
          { required: true, message: "经手人不能为空", trigger: "blur" },
        ],
        expiredate: [
          { required: true, message: "到期时间不能为空", trigger: "blur" },
        ],
        remark: [{ required: true, message: "备注不能为空", trigger: "blur" }],
        usecompany: [
          { required: true, message: "使用单位不能为空", trigger: "blur" },
        ],
        carmodel: [
          { required: true, message: "车型不能为空", trigger: "blur" },
        ],
        carnum: [{ required: true, message: "车号不能为空", trigger: "blur" }],
      },
      bxTypes: [
        {
          value: "1",
          label: "交强险",
        },
        {
          value: "2",
          label: "第三者责任险",
        },
      ],
      bxCompanys: [
        {
          value: "中国人寿",
          label: "中国人寿",
        },
        {
          value: "平安人寿",
          label: "平安人寿",
        },
        {
          value: "太平洋保险",
          label: "太平洋保险",
        },
      ],
    };
  },
  methods: {
    init(id, isAdd, row) {
      this.dataForm.id = id || 0;
      this.visible = true;
      if (isAdd && row) {
        this.addId = this.dataForm.id;
        this.dataForm.carnum = row.number;
        this.dataForm.carmodel = row.model;
      } else {
        this.$nextTick(() => {
          this.$refs["dataForm"].resetFields();
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(
                `/generator/tcarinsurance/info/${this.dataForm.id}`
              ),
              method: "get",
              params: this.$http.adornParams(),
            }).then(({ data }) => {
              if (data && data.code === 0) {
                this.dataForm.carid = data.tCarInsurance.carid;
                this.dataForm.number = data.tCarInsurance.number;
                this.dataForm.bxdate = data.tCarInsurance.bxdate;
                this.dataForm.bxtype = data.tCarInsurance.bxtype;
                this.dataForm.cost = data.tCarInsurance.cost;
                this.dataForm.amount = data.tCarInsurance.amount;
                this.dataForm.company = data.tCarInsurance.company;
                this.dataForm.transactor = data.tCarInsurance.transactor;
                this.dataForm.expiredate = data.tCarInsurance.expiredate;
                this.dataForm.remark = data.tCarInsurance.remark;
                this.dataForm.usecompany = data.tCarInsurance.usecompany;
                this.dataForm.carmodel = data.tCarInsurance.carmodel;
                this.dataForm.carnum = data.tCarInsurance.carnum;
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
              `/generator/tcarinsurance/${this.addId > 0 ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              carid: this.addId || undefined,
              carid: this.dataForm.carid,
              number: this.dataForm.number,
              bxdate: this.dataForm.bxdate,
              bxtype: this.dataForm.bxtype,
              cost: this.dataForm.cost,
              amount: this.dataForm.amount,
              company: this.dataForm.company,
              transactor: this.dataForm.transactor,
              expiredate: this.dataForm.expiredate,
              remark: this.dataForm.remark,
              usecompany: this.dataForm.usecompany,
              carmodel: this.dataForm.carmodel,
            }),
          })
            .then(({ data }) => {
              if (data && data.code === 0) {
                this.$message({
                  message: "操作成功",
                  type: "success",
                  duration: 1500,
                  onClose: () => {
                    this.addId = 0;
                    this.$refs["dataForm"].resetFields();
                    this.visible = false;
                    this.$emit("refreshDataList");
                    this.$emit("closeDialog");
                  },
                });
              } else {
                this.$message.error(data.msg);
              }
            })
            .catch((_) => {});
        }
      });
    },
    // 取消选择
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
