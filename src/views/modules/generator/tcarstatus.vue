<template>
  <el-container>
    <el-header>车辆状态查询</el-header>
    <el-container>
      <el-main>
        <div class="mod-config">
          <el-form
            :inline="true"
            :model="dataForm"
            @keyup.enter.native="getDateList(dataForm.begintime)"
          >
            <span class="dateto" v-text="dataForm.selectText"
              >选择查询月份：</span
            >
            <el-form-item label="">
              <el-date-picker
                v-if="dataForm.isMonth"
                v-model="dataForm.begintime"
                type="month"
                placeholder=""
              >
              </el-date-picker>
              <el-date-picker
                :format="dataForm.begintime + '至' + dataForm.endtime"
                :picker-options="{ firstDayOfWeek: 1 }"
                v-else
                v-model="dataForm.weekVal"
                type="week"
                @change="showDate"
              >
              </el-date-picker>
              <el-form-item>
                <!-- <el-radio v-model="dataForm.monthOrWeek" label="month">按月</el-radio>
              <el-radio v-model="dataForm.monthOrWeek" label="week">按周</el-radio> -->
                <el-switch
                  style="display: block"
                  v-model="dataForm.isMonth"
                  active-color="#13ce66"
                  inactive-color="#ff4949"
                  active-text="按月"
                  inactive-text="按周"
                  @change="onSwitchChange"
                >
                </el-switch>
              </el-form-item>
              <el-form-item>
                <el-button
                  v-text="dataForm.lastText"
                  type="primary"
                  @click="lastDateClick"
                  >上一周</el-button
                >
                <el-button
                  v-text="dataForm.nextText"
                  type="primary"
                  @click="nextDateClick"
                  >下一周</el-button
                >
              </el-form-item>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="getDateList()"
                >查询</el-button
              >
            </el-form-item>
          </el-form>
          <el-table
            :data="dataList"
            ref="tCarStatusTable"
            border
            v-loading="dataListLoading"
            style="width: 100%"
          >
            <el-table-column
              prop="carnum"
              header-align="center"
              align="center"
              label="车辆\日期"
            >
            </el-table-column>
            <el-table-column
              header-align="center"
              align="center"
              v-for="item in dateList"
              :label="item.label"
              :key="item.key"
            >
              <template slot-scope="scope">
                <div
                  v-if="!isHaveBegintime"
                  :style="isChuChe(scope.row, item)"
                  @click="clickToSeeCarRun($event, scope.row)"
                >
                  {{ dateFormat(scope, item) }}
                </div>
                <div
                  v-else
                  :style="{
                    color: rows[`${scope.row.id}`][`${item.key}`].color,
                  }"
                  @click="clickToSeeCarRun($event, scope.row)"
                >
                  {{ rows[`${scope.row.id}`][`${item.key}`].value }}
                </div>
              </template>
            </el-table-column>
          </el-table>
        </div>
        <!-- 弹窗, 新增 / 修改 -->
        <add-or-update
          v-if="addOrUpdateVisible"
          ref="addOrUpdate"
          @refreshDataList="getDataList"
        ></add-or-update>
      </el-main>
    </el-container>
  </el-container>
</template>
<script>
import AddOrUpdate from "./tcarrun-add-or-update";

let that;
export default {
  data() {
    return {
      dataForm: {
        begintime: "",
        endtime: "",
        isMonth: true,
        lastText: "上一月",
        nextText: "下一月",
        selectText: "选择查询月份：",
        weekVal: "",
        dateType: "month",
      },
      year: "",
      spanColor: "",
      row: {},
      rows: [],
      dataList: [],
      dateList: [],
      dateObj: {},
      dataListLoading: false,
      pageIndex: 1,
      pageSize: 30,
      addOrUpdateVisible: false,
      isHaveBegintime: false,
    };
  },
  created() {
    that = this;
  },
  activated() {
    this.getDateList();
    this.getDataList();
  },
  components: {
    AddOrUpdate,
  },
  methods: {
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/generator/tcarcar/tcarstatus"),
        method: "get",
        params: this.$http.adornParams({
          begintime: this.dataForm.begintime,
          endtime: this.dataForm.endtime,
          type: this.dataForm.dateType,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.list;
          console.log(data);
          if (data.dlist != null) {
            this.rows = data.dlist;
            this.dateList = data.dates;
            this.isHaveBegintime = true;
          }
          if (this.dataForm.begintime == '' || this.dataForm.endtime == '' ) {
            this.dataForm.begintime = data.begin
            this.dataForm.endtime = data.end
          }
        } else {
          this.dataList = [];
        }
        this.dataListLoading = false;
      });
    },
    getDateList(d) {
      if (d != null && d != "") {
        this.dateList.splice(0, this.dateList.length);
        return this.getDataList();
      } else {
        this.dateList.splice(0, this.dateList.length);
        var date = new Date();
        var year = date.getFullYear();
        var month = date.getMonth() + 1;
        var m = month < 10 ? "0" + month : month;
        var day = this.getMonthDate(year, m);
        for (let i = 1; i <= day; i++) {
          this.dateList.push({
            label: m + "-" + (i < 10 ? "0" + i : i),
            value: "",
            date: year + "-" + m + "-" + (i < 10 ? "0" + i : i),
            color: "",
            key: i,
          });
        }
        this.year = date.getFullYear();
      }
    },
    getMonthDate(year, month) {
      var date = new Date(year, month, 0);
      return date.getDate();
    },
    // 点击每一项数据如果是已出车的，可以看到出车信息
    clickToSeeCarRun(e, row) {
      if (e.target.innerText == "出车") {
        this.addOrUpdateVisible = true;
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(row);
        });
      }
    },
    dFormat(date) {
      let year = date.getFullYear();
      let month = date.getMonth() + 1;
      month = month < 10 ? "0" + month : month;
      let day = date.getDate();
      day = day < 10 ? "0" + day : day;
      return year + "-" + month + "-" + day;
    },
    onSwitchChange(val) {
      const dataForm = this.dataForm;
      let begin;
      let end;
      if (val) {
        dataForm.lastText = "上一月";
        dataForm.nextText = "下一月";
        dataForm.monthOrWeek = "month";
        dataForm.selectText = "选择查询月份：";
        dataForm.dateType = "month";
        begin = this.splitDate(
            this.dayjs(this.dataForm.begintime).startOf("month").$d
      );
       end = this.splitDate(this.dayjs(begin).endOf("month").$d);
      } else {
        dataForm.lastText = "上一周";
        dataForm.nextText = "下一周";
        dataForm.monthOrWeek = "week";
        dataForm.selectText = "选择查询周：";
        dataForm.dateType = "week";
        begin = this.splitDate(this.dayjs(new Date()).startOf("week").add(1,'day').$d);
        end = this.splitDate(this.dayjs(begin).add(6, "day").$d);
        this.dataForm.weekVal = begin
      }
      this.dataForm.begintime = begin;
      this.dataForm.endtime = end;
      return this.getDataList();
    },
    showDate() {
      const dataForm = this.dataForm;
      let start = this.dayjs(dataForm.weekVal).subtract(1, "day").$d;
      let end = this.dayjs(start).add(6, "day").$d;
      dataForm.begintime = this.splitDate(start);
      dataForm.endtime = this.splitDate(end);
    },
    lastDateClick() {
      if (this.dataForm.begintime) {
        let start = this.dayjs(this.dataForm.begintime).subtract(1, "month").startOf("month").$d;
        let end = this.dayjs(start).endOf("month").$d;
        if (this.dataForm.dateType === "week") {
         start = this.dayjs(this.dataForm.begintime).subtract(7, "day").$d;
        end = this.dayjs(start).add(6, "day").$d;
          this.dataForm.weekVal = this.dayjs(this.dataForm.weekVal).subtract(
            7,
            "day"
          );
        }
        this.dataForm.begintime = this.splitDate(start);
        this.dataForm.endtime = this.splitDate(end);
        return this.getDataList();
      }
    },
    nextDateClick() {
      if (this.dataForm.begintime) {
        let start = this.dayjs(this.dataForm.begintime).add(1, "month").startOf("month").$d;
        let end = this.dayjs(start).endOf("month").$d;
        if (this.dataForm.dateType === "week") {
          start = this.dayjs(this.dataForm.begintime).add(7, "day").$d;
          end = this.dayjs(start).add(6, "day").$d;
          this.dataForm.weekVal = this.dayjs(this.dataForm.weekVal).add(
            7,
            "day"
          );
        }
        this.dataForm.begintime = this.splitDate(start);
        this.dataForm.endtime = this.splitDate(end);
        return this.getDateList(this.dataForm.begintime);
      }
    },
    splitDate(date) {
      return this.dayjs(date).format("YYYY-MM-DD");
    },
  },
  filters: {},
  computed: {
    dateFormat() {
      return (val, item) => {
        let row = val.row;
        let begintime = val.row.begintime;
        if (row.status != null && begintime == item.date && row.status == 1) {
          return "出车";
        } else {
          return "空闲";
        }
      };
    },
    isChuChe() {
      return (row, item) => {
        if (
          row.status != null &&
          row.begintime == item.date &&
          row.status == 1
        ) {
          return {
            color: "red",
          };
        } else {
          return {
            color: "green",
          };
        }
      };
    },
  },
  watch: {
    "dataForm.begintime": {
      handler(newVal, oldVal) {
        if (this.dataForm.dateType == "month") {
          var begin = this.splitDate(
            this.dayjs(this.dataForm.begintime).startOf("month").$d
          );
          var end = this.splitDate(this.dayjs(begin).endOf("month").$d);
          this.dataForm.begintime = begin;
          this.dataForm.endtime = end;
        }
      },
    },
    // "dataForm.dateType": {
    //   handler(newVal,oldVal) {
    //     if (newVal == 'week') {
    //       let start = this.dayjs(new Date()).startOf("week").$d+1;
    //       console.log(start);
    //       let end = this.dayjs(start).add(6, "day").$d;
    //       this.dataForm.begintime = this.splitDate(start);
    //       this.dataForm.endtime = this.splitDate(end);
    //       this.getDateList()
    //     }
    //   }
    // }
  },
};
</script>
<style scoped>
.el-header,
.el-footer {
  background-color: #b3c0d1;
  color: #333;
  text-align: center;
  line-height: 60px;
}

.el-main {
  background-color: #e9eef3;
  color: #333;
}

.el-container > .el-container {
  height: 70%;
}

body > .el-container {
  margin-bottom: 40px;
}

.typetree {
  margin-top: 1%;
}

.dateto {
  line-height: 36px;
}
</style>
