<template>
  <el-dialog
    :title="!dataForm.id ? '车辆档案信息' : '车辆档案信息修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    width="60%"
    @close="onDialogClosed"
  >
    <el-tabs type="border-card" v-model="activeName">
      <el-tab-pane label="车辆信息" name="car">
        <!-- 弹窗, 新增 / 修改 -->
        <!-- <tcarcar-add ref="addCar" @refreshDataList="getDataList"></tcarcar-add> -->
        <el-form
          :model="dataForm"
          :rules="dataRule"
          ref="dataForm"
          @keyup.enter.native="dataFormSubmit()"
          label-width="80px"
        >
          <el-row>
            <el-col :span="8">
              <div class="grid-content">
                <el-form-item label="车牌" prop="number" label-position="right">
                  <el-input
                    v-model="dataForm.number"
                    placeholder="车牌"
                  ></el-input>
                </el-form-item>
                <el-form-item label="车辆种类" label-position="right">
                  <el-select v-model="dataForm.type" placeholder="">
                    <el-option
                      v-for="item in carTypes"
                      :key="item.value"
                      :label="item.label"
                      :value="item.value"
                    >
                    </el-option>
                  </el-select>
                </el-form-item>
                <el-form-item
                  label="载客人数"
                  prop="seating"
                  label-position="right"
                >
                  <el-input
                    type="number"
                    v-model="dataForm.seating"
                    placeholder="载客人数"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label-width="120px"
                  label="保养里程公里"
                  prop="servicemileage"
                  label-position="right"
                >
                  <el-input
                    type="number"
                    v-model="dataForm.servicemileage"
                    placeholder="保养里程公里"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="车架号"
                  prop="framenumber"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.framenumber"
                    placeholder="车架号"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="购置时间"
                  prop="buydate"
                  label-position="right"
                >
                  <el-date-picker
                    v-model="dataForm.buydate"
                    type="date"
                    align="right"
                    placeholder="购置时间"
                    style="width: 160px"
                    value-format="yyyy-MM-dd hh:mm:ss"
                  >
                  </el-date-picker>
                </el-form-item>
                <el-form-item
                  label="是否停用"
                  prop="isstop"
                  label-position="right"
                >
                  <el-radio v-model="dataForm.isstop" :label="1">是</el-radio>
                  <el-radio v-model="dataForm.isstop" :label="0">否</el-radio>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="8">
              <div class="grid-content">
                <el-form-item
                  label="车辆品牌"
                  prop="brand"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.brand"
                    placeholder="车辆品牌"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="车辆颜色"
                  prop="color"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.color"
                    placeholder="车辆颜色"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label-width="120px"
                  label="油耗(L/百公里)"
                  prop="fuelconsumption"
                  label-position="right"
                >
                  <el-input
                    type="number"
                    v-model="dataForm.fuelconsumption"
                    placeholder="油耗(L/百公里)"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="保养周期"
                  prop="servicecycle"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.servicecycle"
                    placeholder="保养周期"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label-width="120px"
                  label="供应商(4S店)"
                  prop="vendor"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.vendor"
                    placeholder="供应商"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="注册单位"
                  prop="company"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.company"
                    placeholder="注册单位"
                  ></el-input>
                </el-form-item>
              </div>
            </el-col>
            <el-col :span="8">
              <div class="grid-content">
                <el-form-item label="车型" prop="model" label-position="right">
                  <el-input
                    v-model="dataForm.model"
                    placeholder="车型"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="载重(吨)"
                  prop="load"
                  label-position="right"
                >
                  <el-input
                    type="number"
                    v-model="dataForm.load"
                    placeholder="载重(吨)"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label-width="120px"
                  label="初始里程公里"
                  prop="initmileage"
                  label-position="right"
                >
                  <el-input
                    type="number"
                    v-model="dataForm.initmileage"
                    placeholder="初始里程公里"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="发动机号"
                  prop="enginenumber"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.enginenumber"
                    placeholder="发动机号"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label-width="120px"
                  label="购入价格元"
                  prop="buyprice"
                  label-position="right"
                >
                  <el-input
                    type="number"
                    v-model="dataForm.buyprice"
                    placeholder="购入价格元"
                  ></el-input>
                </el-form-item>
                <el-form-item
                  label="使用单位"
                  prop="usecompany"
                  label-position="right"
                >
                  <el-input
                    v-model="dataForm.usecompany"
                    placeholder="使用单位"
                  ></el-input>
                </el-form-item>
              </div>
            </el-col>
          </el-row>
          <el-form-item label="备注" prop="remark" style="width: 80%">
            <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
          </el-form-item>
        </el-form>
        <!-- 司机 -->
        <h3>专职司机</h3>
        <tcardriver ref="tCarDriver"></tcardriver>
      </el-tab-pane>
      <el-tab-pane label="保险记录">
        <tcarinsurance ref="tCarInsurance"></tcarinsurance>
      </el-tab-pane>
      <el-tab-pane label="年检记录">
        <tcarinspection ref="tCarInspection"></tcarinspection>
      </el-tab-pane>
      <!-- <tcarcar-add></tcarcar-add>
            <span slot="footer" class="dialog-footer">
              <el-button @click="dialogVisible = false">取 消</el-button>
              <el-button type="primary" @click="carAdd">确 定</el-button>
            </span> -->
    </el-tabs>
    <div class="dialog-footer" v-if="activeName === 'car'">
      <el-button @click="onDialogClosed">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{
        dataForm.id ? "保存" : "确定"
      }}</el-button>
    </div>
  </el-dialog>
</template>

<script>
import Tcardriver from "./tcardriver.vue";
import Tcarinspection from "./tcarinspection.vue";
import Tcarinsurance from "./tcarinsurance.vue";

export default {
  data() {
    return {
      visible: false,
      activeName: "car",
      dataForm: {
        id: 0,
        number: "",
        brand: "宝马",
        model: "a",
        type: "1",
        color: "黑色",
        load: "1",
        seating: "6",
        fuelconsumption: "100",
        initmileage: "200",
        servicemileage: "300",
        servicecycle: "30",
        enginenumber: "dfwaf666",
        framenumber: "jhrdy999",
        vendor: "jtdjdd",
        buyprice: "600000",
        buydate: "",
        company: "单位1",
        deptid: "",
        deptidName: "",
        usecompany: "使用单位1",
        usedept: "",
        status: "1",
        remark: "jkdtjdr",
        isstop: 0,
      },
      carTypes: [
        {
          value: 1,
          label: "小轿车",
        },
        {
          value: 2,
          label: "大客车",
        },
      ],
      dataRule: {
        number: [{ required: true, message: "车牌不能为空", trigger: "blur" }],
        brand: [
          { required: true, message: "车辆品牌不能为空", trigger: "blur" },
        ],
        model: [{ required: true, message: "车型不能为空", trigger: "blur" }],
        type: [
          { required: true, message: "车辆类型不能为空", trigger: "blur" },
        ],
        color: [
          { required: true, message: "车辆颜色不能为空", trigger: "blur" },
        ],
        load: [
          { required: true, message: "载重(吨)不能为空", trigger: "blur" },
        ],
        seating: [
          { required: true, message: "载客人数不能为空", trigger: "blur" },
        ],
        fuelconsumption: [
          {
            required: true,
            message: "油耗(L/百公里)不能为空",
            trigger: "blur",
          },
        ],
        initmileage: [
          {
            required: true,
            message: "初始里程(公里)不能为空",
            trigger: "blur",
          },
        ],
        servicemileage: [
          {
            required: true,
            message: "保养里程(公里)不能为空",
            trigger: "blur",
          },
        ],
        servicecycle: [
          { required: true, message: "保养周期不能为空", trigger: "blur" },
        ],
        enginenumber: [
          { required: true, message: "发动机号不能为空", trigger: "blur" },
        ],
        framenumber: [
          { required: true, message: "车架号不能为空", trigger: "blur" },
        ],
        vendor: [
          { required: true, message: "供应商不能为空", trigger: "blur" },
        ],
        buyprice: [
          { required: true, message: "购入价格(元)不能为空", trigger: "blur" },
        ],
        buydate: [
          { required: true, message: "购置时间不能为空", trigger: "blur" },
        ],
        company: [
          { required: true, message: "注册单位不能为空", trigger: "blur" },
        ],
        deptid: [
          { required: true, message: "所属部门不能为空", trigger: "blur" },
        ],
        deptidName: [
          { required: true, message: "部门不能为空", trigger: "blur" },
        ],
        usecompany: [
          { required: true, message: "使用单位不能为空", trigger: "blur" },
        ],
        usedept: [
          { required: true, message: "使用部门不能为空", trigger: "blur" },
        ],
        status: [
          { required: true, message: "当前状态不能为空", trigger: "blur" },
        ],
        remark: [{ required: true, message: "备注不能为空", trigger: "blur" }],
        isstop: [
          { required: true, message: "是否停用不能为空", trigger: "blur" },
        ],
      },
    };
  },
  components: {
    Tcarinsurance,
    Tcarinspection,
    Tcardriver,
  },
  mounted() {},
  methods: {
    init(row, isUpdate) {
      this.visible = true;
      this.dataForm.id = row ? row.id : 0;
      this.$nextTick(() => {
        this.$refs.tCarInspection.init(row, isUpdate);
        this.$refs.tCarInsurance.init(row, isUpdate);
        this.$refs.tCarDriver.init(this.dataForm.id, isUpdate, true);
        this.$refs["dataForm"].resetFields();
        this.$http({
          url: this.$http.adornUrl(
            `/generator/tcarcar/info/${this.dataForm.id}`
          ),
          method: "get",
          params: this.$http.adornParams(),
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.dataForm.number = data.tCarCar.number;
            this.dataForm.brand = data.tCarCar.brand;
            this.dataForm.model = data.tCarCar.model;
            this.dataForm.type = data.tCarCar.type;
            this.dataForm.color = data.tCarCar.color;
            this.dataForm.load = data.tCarCar.load;
            this.dataForm.seating = data.tCarCar.seating;
            this.dataForm.fuelconsumption = data.tCarCar.fuelconsumption;
            this.dataForm.initmileage = data.tCarCar.initmileage;
            this.dataForm.servicemileage = data.tCarCar.servicemileage;
            this.dataForm.servicecycle = data.tCarCar.servicecycle;
            this.dataForm.enginenumber = data.tCarCar.enginenumber;
            this.dataForm.framenumber = data.tCarCar.framenumber;
            this.dataForm.vendor = data.tCarCar.vendor;
            this.dataForm.buyprice = data.tCarCar.buyprice;
            this.dataForm.buydate = data.tCarCar.buydate;
            this.dataForm.company = data.tCarCar.company;
            this.dataForm.deptid = data.tCarCar.deptid;
            this.dataForm.deptidName = data.tCarCar.deptidName;
            this.dataForm.usecompany = data.tCarCar.usecompany;
            this.dataForm.usedept = data.tCarCar.usedept;
            this.dataForm.status = data.tCarCar.status;
            this.dataForm.remark = data.tCarCar.remark;
            this.dataForm.isstop = data.tCarCar.isstop;
          }
        });
      });
    },
    // 表单提交
    dataFormSubmit() {
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/generator/tcarcar/${!this.dataForm.id ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              number: this.dataForm.number,
              brand: this.dataForm.brand,
              model: this.dataForm.model,
              type: this.dataForm.type,
              color: this.dataForm.color,
              load: this.dataForm.load,
              seating: this.dataForm.seating,
              fuelconsumption: this.dataForm.fuelconsumption,
              initmileage: this.dataForm.initmileage,
              servicemileage: this.dataForm.servicemileage,
              servicecycle: this.dataForm.servicecycle,
              enginenumber: this.dataForm.enginenumber,
              framenumber: this.dataForm.framenumber,
              vendor: this.dataForm.vendor,
              buyprice: this.dataForm.buyprice,
              buydate: this.dataForm.buydate,
              company: this.dataForm.company,
              deptid: this.dataForm.deptid,
              deptidName: this.dataForm.deptidName,
              usecompany: this.dataForm.usecompany,
              usedept: this.dataForm.usedept,
              status: this.dataForm.status,
              remark: this.dataForm.remark,
              isstop: this.dataForm.isstop,
            }),
          })
            .then(({ data }) => {
              if (data && data.code === 0) {
                var ids = [101,106,107]
                var type = this.dataForm.type == 1 ? '小轿车' : '大货车'
                var datas = [this.dataForm.usecompany,type,this.dataForm.company]
                for (let index = 0; index < ids.length; index++) {
                  this.$http({
                    url: this.$http.adornUrl('/generator/tcardept/save'),
                    method: "post",
                    data: this.$http.adornData({
                      parentId: ids[index],
                      deptName: datas[index]
                    })
                  })
                }
                this.$message({
                  message: "操作成功",
                  type: "success",
                  duration: 1500,
                  onClose: () => {
                    this.$refs.tCarInspection.init();
                    this.$refs.tCarInsurance.init();
                    this.$refs.tCarDriver.init();
                    this.visible = false;
                    this.$emit("refreshDataList",false,this.dataForm.id <= 0 ? true : false);
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
    clearFormData() {
      for (let i in this.dataForm) {
        delete this.dataForm[i];
      }
    },
    // 取消修改
    onDialogClosed() {
      this.$refs.tCarInspection.init();
      this.$refs.tCarInsurance.init();
      this.$refs.tCarDriver.init();
      this.visible = false;
      this.$emit("tCarUpdateClosed");
    },
  },
};
</script>
<style scoped>
.el-row {
  margin-bottom: 20px;
  &:last-child {
    margin-bottom: 0;
  }
}
.el-col {
  border-radius: 4px;
}
.bg-purple-dark {
  background: #99a9bf;
}
.bg-purple {
  background: #d3dce6;
}
.bg-purple-light {
  background: #e5e9f2;
}
.grid-content {
  border-radius: 4px;
  min-height: 36px;
}
.row-bg {
  padding: 10px 0;
  background-color: #f9fafc;
}
/deep/ .el-dialog__header {
  width: 100%;
  height: 80px;
  line-height: 0px;
}
/deep/ .el-dialog__body {
  position: relative;
}
.dialog-footer {
  position: absolute;
  right: 2%;
  bottom: 1%;
}

.el-tabs {
  margin-bottom: 40px;
}
</style>
