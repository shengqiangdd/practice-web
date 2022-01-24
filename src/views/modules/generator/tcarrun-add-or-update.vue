<template>
  <el-dialog
    :title="!dataForm.id ? '派车信息添加' : '派车信息修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
  >
    <el-form
      :model="dataForm"
      :rules="dataRule"
      ref="dataForm"
      @keyup.enter.native="dataFormSubmit()"
      label-width="100px"
    >
      <el-row>
        <el-col :span="10">
          <el-form-item label="用车人ID" prop="userid">
            <el-input v-model="dataForm.userid" placeholder="用车人"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="用车人单位" prop="deptid">
            <el-select v-model="dataForm.deptid">
              <el-option
                v-for="item in useCarPeopleList"
                :key="item.deptid"
                :label="item.deptName"
                :value="item.deptid"
              >
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="10">
          <el-form-item label="开始时间" prop="begintime">
            <el-date-picker
              v-model="dataForm.begintime"
              type="date"
              align="right"
              placeholder="开始时间"
              style="width: 100%"
              value-format="yyyy-MM-dd HH:mm:ss"
            >
            </el-date-picker>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="结束时间" prop="endtime">
            <el-date-picker
              v-model="dataForm.endtime"
              type="date"
              align="right"
              placeholder="结束时间"
              style="width: 100%"
              value-format="yyyy-MM-dd HH:mm:ss"
            >
            </el-date-picker>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="10">
          <el-form-item label="车号" prop="carid">
            <el-input v-model="dataForm.carid" placeholder="车号"></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="单号" prop="number">
            <el-input v-model="dataForm.number" placeholder="单号"></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="21">
          <el-form-item label="用车事由" prop="reason">
            <el-input
              type="textarea"
              v-model="dataForm.reason"
              placeholder="用车事由"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="10">
          <el-form-item label="起始地" prop="beginplace">
            <el-input
              v-model="dataForm.beginplace"
              placeholder="起始地"
            ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="目的地" prop="endplace">
            <el-input
              v-model="dataForm.endplace"
              placeholder="目的地"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="10">
          <el-form-item label="司机" prop="driver">
            <el-select v-model="dataForm.driverId">
              <el-option
                v-for="item in driverList"
                :key="item.id"
                :label="item.name"
                :value="item.id"
              >
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="司机电话" prop="drivertel">
            <el-input
              type="number"
              v-model="dataForm.drivertel"
              placeholder="司机电话"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="10">
          <el-form-item label="开始里程数" prop="beginnumber">
            <el-input
              v-model="dataForm.beginnumber"
              placeholder="开始里程数"
            ></el-input>
          </el-form-item>
        </el-col>
        <el-col :span="10">
          <el-form-item label="结束里程数" prop="endnumber">
            <el-input
              type="number"
              v-model="dataForm.endnumber"
              placeholder="结束里程数"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>

      <el-row>
        <el-col :span="10">
          <el-form-item label="行驶里程数(公里)" prop="mileage">
            <el-input
              type="number"
              v-model="dataForm.mileage"
              placeholder="行驶里程数(公里)"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="21">
          <el-form-item label="备注" prop="remark">
            <el-input
              type="textarea"
              v-model="dataForm.remark"
              placeholder="备注"
            ></el-input>
          </el-form-item>
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="21">
          <el-form-item label="状态" prop="status">
            <el-select v-model="dataForm.status">
              <el-option
                v-for="item in statusList"
                :key="item.id"
                :label="item.name"
                :value="item.id"
              >
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
      </el-row>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="onDialogClosed">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">{{
        dataForm.id ? "保存" : "确定"
      }}</el-button>
    </span>
  </el-dialog>
</template>

<script>
import selectDriver from "./selectDriver.vue";
export default {
  data() {
    return {
      visible: false,
      dataForm: {
        id: 0,
        carid: "1",
        userid: "1",
        username: "",
        number: "",
        begintime: "",
        planreturntime: "",
        endtime: "",
        deptid: 120,
        deptname: "",
        party: "",
        driver: "",
        drivertel: "",
        beginplace: "南宁",
        endplace: "广州",
        reason: "fawfawfwa",
        beginnumber: "200",
        endnumber: "300",
        mileage: "400",
        remark: "dawdagaw",
        type: "",
        status: "1",
        drivername: "",
        driverId: "",
      },
      dataListLoading: false,
      statusList: [
        {
          id: 1,
          name: "出车",
        },
        {
          id: 2,
          name: "空闲",
        },
      ],
      driverList: [],
      useCarPeopleList: [],
      dataRule: {
        carid: [{ required: true, message: "车号不能为空", trigger: "blur" }],
        userid: [
          { required: true, message: "用车人不能为空", trigger: "blur" },
        ],
        username: [
          { required: true, message: "姓名不能为空", trigger: "blur" },
        ],
        number: [
          { required: true, message: "申请单号不能为空", trigger: "blur" },
        ],
        begintime: [
          { required: true, message: "开始时间不能为空", trigger: "blur" },
        ],
        planreturntime: [
          { required: true, message: "预计回车时间不能为空", trigger: "blur" },
        ],
        endtime: [
          { required: true, message: "结束时间不能为空", trigger: "blur" },
        ],
        deptid: [
          { required: true, message: "用车人单位不能为空", trigger: "blur" },
        ],
        deptname: [
          { required: true, message: "部门不能为空", trigger: "blur" },
        ],
        party: [
          { required: true, message: "随行人员不能为空", trigger: "blur" },
        ],
        driverId: [
          { required: true, message: "司机不能为空", trigger: "blur" },
        ],
        drivertel: [
          { required: true, message: "司机电话不能为空", trigger: "blur" },
        ],
        beginplace: [
          { required: true, message: "起始地不能为空", trigger: "blur" },
        ],
        endplace: [
          { required: true, message: "目的地不能为空", trigger: "blur" },
        ],
        reason: [
          { required: true, message: "用车事由不能为空", trigger: "blur" },
        ],
        beginnumber: [
          { required: true, message: "开始里程不能为空", trigger: "blur" },
        ],
        endnumber: [
          { required: true, message: "结束里程不能为空", trigger: "blur" },
        ],
        mileage: [
          { required: true, message: "行驶里程不能为空", trigger: "blur" },
        ],
        remark: [{ required: true, message: "备注不能为空", trigger: "blur" }],
        type: [
          {
            required: true,
            message: "类型(1、普通类型2、借出类型)不能为空",
            trigger: "blur",
          },
        ],
        status: [{ required: true, message: "状态不能为空", trigger: "blur" }],
      },
    };
  },
  components: { selectDriver },
  methods: {
    init(row) {
      this.dataForm.id = row ? row.carid : 0;
      this.$nextTick(() => {
        // this.$refs["dataForm"].resetFields();
        this.$http({
          url: this.$http.adornUrl(
            `/generator/tcarrun/info/${this.dataForm.id}`
          ),
          method: "get",
          params: this.$http.adornParams(),
        })
          .then(({ data }) => {
            if (data && data.code === 0) {
              this.dataForm.carid = data.tCarRun.carid;
              this.dataForm.userid = data.tCarRun.userid;
              this.dataForm.username = data.tCarRun.username;
              this.dataForm.number = data.tCarRun.number;
              this.dataForm.begintime = data.tCarRun.begintime;
              this.dataForm.planreturntime = data.tCarRun.planreturntime;
              this.dataForm.endtime = data.tCarRun.endtime;
              this.dataForm.deptid = data.tCarRun.deptid;
              this.dataForm.deptname = data.tCarRun.deptname;
              this.dataForm.party = data.tCarRun.party;
              this.dataForm.driver = data.tCarRun.driver;
              this.dataForm.drivertel = data.tCarRun.drivertel;
              this.dataForm.beginplace = data.tCarRun.beginplace;
              this.dataForm.endplace = data.tCarRun.endplace;
              this.dataForm.reason = data.tCarRun.reason;
              this.dataForm.beginnumber = data.tCarRun.beginnumber;
              this.dataForm.endnumber = data.tCarRun.endnumber;
              this.dataForm.mileage = data.tCarRun.mileage;
              this.dataForm.remark = data.tCarRun.remark;
              this.dataForm.type = data.tCarRun.type;
              this.dataForm.status = data.tCarRun.status;
              this.useCarPeopleList = data.useList;
            }
          })
          .catch((_) => {
            this.dataForm.begintime = "";
            this.dataForm.planreturntime = "";
            this.dataForm.endtime = "";
            this.dataForm.number = "";
          });
      });
      if (this.driverList.length <= 0 && row) {
        this.getDriverList(row);
      } else {
        this.getDriverList();
      }
      this.visible = true;
    },
    // 表单提交
    dataFormSubmit() {
      var driver = {};
      if (this.dataForm.driverId > 0) {
        driver = this.driverList.filter(
          (d) => this.dataForm.driverId == d.id
        )[0];
      }
      this.$refs["dataForm"].validate((valid) => {
        if (valid) {
          this.$http({
            url: this.$http.adornUrl(
              `/generator/tcarrun/${!this.dataForm.id ? "save" : "update"}`
            ),
            method: "post",
            data: this.$http.adornData({
              id: this.dataForm.id || undefined,
              carid: this.dataForm.carid,
              userid: this.dataForm.userid,
              username: this.dataForm.username,
              number: this.dataForm.number,
              begintime: this.dataForm.begintime,
              planreturntime: this.dataForm.planreturntime,
              endtime: this.dataForm.endtime,
              deptid: this.dataForm.deptid,
              deptname: this.dataForm.deptname,
              party: this.dataForm.party,
              driver: driver ? driver.name : "",
              drivertel: this.dataForm.drivertel,
              beginplace: this.dataForm.beginplace,
              endplace: this.dataForm.endplace,
              reason: this.dataForm.reason,
              beginnumber: this.dataForm.beginnumber,
              endnumber: this.dataForm.endnumber,
              mileage: this.dataForm.mileage,
              remark: this.dataForm.remark,
              type: this.dataForm.type,
              status: this.dataForm.status,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.visible = false;
                  this.$emit("refreshDataList");
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        }
      });
    },
    getDriverList(val) {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl(`/generator/tcardriver/list`),
        method: "get",
        params: this.$http.adornParams(),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.driverList = data.page.records;
          var driver = {};
          if (val) {
            driver = this.driverList.filter((d) => val.driver == d.name)[0];
          } else {
            driver = this.driverList.filter((d) => d.id == 3)[0];
          }
          this.dataForm.driverId = driver ? driver.id : "";
        } else {
          this.driverList = [];
        }
        this.dataListLoading = false;
      });
    },
    onDialogClosed() {
      this.dataForm.id = 0;
      this.visible = false;
    },
  },
  watch: {
    dataForm: {
      handler(newVal, oldVal) {
        let driver = this.driverList.filter((d) => d.id == newVal.driverId)[0];
        this.dataForm.drivertel =
          driver && driver.tel != null ? driver.tel : "";
      },
      deep: true,
    },
  },
};
</script>
<style scoped>
/deep/ .el-col {
  margin-left: 4%;
}
</style>
