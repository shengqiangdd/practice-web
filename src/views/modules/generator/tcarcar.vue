<template>
  <el-container>
    <el-header>车辆台账</el-header>
    <el-container>
      <el-aside width="200px">
        <div class="typetree">
          <el-tree
            :data="typeTree"
            node-key="deptid"
            :props="defaultProps"
            :default-expanded-keys="nodeIds"
            ref="tree"
            @node-click="handleNodeClick"
            @node-expand="handleNodeExpand"
            @node-collapse="handleNodeCollapse"
            accordion
          ></el-tree>
        </div>
      </el-aside>
      <el-main>
        <div class="mod-config">
          <el-form
            :inline="true"
            :model="dataForm"
            @keyup.enter.native="getDataList()"
          >
            <el-form-item label="车号">
              <el-input v-model="dataForm.number" placeholder="车号"></el-input>
            </el-form-item>
            <el-form-item label="车型">
              <el-input v-model="dataForm.type" placeholder="车型"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="getDataList()">查询</el-button>
            </el-form-item>
          </el-form>
          <el-form :inline="true">
            <el-form-item>
              <el-button
                v-if="isAuth('generator:tcarcar:save')"
                type="primary"
                @click="addOrUpdateHandle()"
                >新增</el-button
              >
              <el-button @click="onSearch()">高级查询</el-button>
              <el-button @click="getDataList((isFlush = true))">刷新</el-button>
              <el-button
                v-if="isAuth('generator:tcarcar:delete')"
                type="danger"
                @click="deleteHandle()"
                :disabled="selectComputed.length <= 0"
                >删除选中</el-button
              >
              <el-button
                type="danger"
                @click="cancelSelected"
                :disabled="selectComputed.length <= 0"
                >取消选中</el-button
              >
            </el-form-item>
          </el-form>
          <el-table
            :data="dataList"
            border
            v-loading="dataListLoading"
            @selection-change="selectionChangeHandle"
            ref="tcarTable"
            style="width: 100%"
          >
            <el-table-column
              type="selection"
              header-align="center"
              align="center"
              width="50"
            >
              <template slot-scope="scope">
                <el-checkbox v-model="scope.row.checked"></el-checkbox>
              </template>
            </el-table-column>
            <el-table-column
              prop="id"
              header-align="center"
              align="center"
              label="序号"
            >
            </el-table-column>
            <el-table-column
              prop="number"
              header-align="center"
              align="center"
              label="车牌"
            >
            </el-table-column>
            <el-table-column
              prop="brand"
              header-align="center"
              align="center"
              label="车辆品牌"
            >
            </el-table-column>
            <el-table-column
              prop="type"
              header-align="center"
              align="center"
              label="车辆类型"
            >
              <template slot-scope="scope">
                {{ scope.row.type == 1 ? "小轿车" : "大货车" }}
              </template>
            </el-table-column>
            <el-table-column
              prop="seating"
              header-align="center"
              align="center"
              label="载客人数"
            >
            </el-table-column>
            <el-table-column
              prop="inspection.expiredate"
              header-align="center"
              align="center"
              label="年检至"
            >
              <template slot-scope="scope">
                <div :style="checkInspection(scope.row)">
                  {{
                    scope.row.inspection ? scope.row.inspection.expiredate : ""
                  }}
                </div>
              </template>
            </el-table-column>
            <el-table-column
              prop="insurance.expiredate"
              header-align="center"
              align="center"
              label="保险至"
            >
              <template slot-scope="scope">
                <div :style="checkInsurance(scope.row)">
                  {{
                    scope.row.insurance ? scope.row.insurance.expiredate : ""
                  }}
                </div>
              </template>
            </el-table-column>
            <el-table-column
              fixed="right"
              header-align="center"
              align="center"
              width="150"
              label="操作"
            >
              <template slot-scope="scope">
                <el-button
                  type="text"
                  size="small"
                  @click="addOrUpdateHandle(scope.row, (isUpdate = true))"
                  >修改</el-button
                >
                <el-button
                  type="text"
                  size="small"
                  @click="deleteHandle(scope.row.id)"
                  >删除</el-button
                >
              </template>
            </el-table-column>
          </el-table>
          <el-pagination
            @size-change="sizeChangeHandle"
            @current-change="currentChangeHandle"
            :current-page="pageIndex"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="pageSize"
            :total="totalCount"
            layout="total, sizes, prev, pager, next, jumper"
          >
          </el-pagination>

          <!-- 添加或修改弹窗 -->
          <add-or-update
            v-if="addOrUpdateVisible"
            ref="addOrUpdate"
            @refreshDataList="getDataList"
            @tCarUpdateClosed="dialogToClose"
          ></add-or-update>

          <!-- 高级查询 -->
          <tcarcar-search
            v-if="searchVisible"
            :dataForm="dataForm"
            @dataList="getDataList"
            @toCloseSearch="searchVisible = false"
          ></tcarcar-search>
        </div>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import AddOrUpdate from "./tcarcar-add-or-update";
import TcarcarSearch from "./tcarcar-search.vue";
export default {
  data() {
    return {
      dataForm: {
        number: "",
        brand: "",
        model: "",
        seating: "",
        company: "",
        usecompany: "",
        type: null,
        buydate1: null,
        buydate2: null,
        ntype: 0,
      },
      sum: 0,
      isUpdate: false,
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      totalCount: 0,
      currentCount: 0,
      dialogVisible: false,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      searchVisible: false,
      activeName: "car",
      typeTree: [],
      nodeIds: [],
      isFlush: false,
      defaultProps: {
        children: "children",
        label: "label",
      },
    };
  },
  components: {
    AddOrUpdate,
    TcarcarSearch,
  },
  mounted() {
    // if (this.dataList.length <= 0) {
    //   this.clearFormData();
    //   this.getDataList();
    // }
    // this.getDataList();
  },
  activated() {
    this.getDataList();
  },
  methods: {
    // 获取数据列表
    getDataList(flag) {
      if (flag) {
        this.pageIndex = 1;
      }
      if (this.isFlush) {
        this.clearDataForm();
        this.isFlush = false;
      }
      this.isUpdate = false;
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/generator/tcarcar/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          number: this.dataForm.number,
          brand: this.dataForm.brand,
          model: this.dataForm.model,
          seating: this.dataForm.seating,
          buydate1: this.dataForm.buydate1,
          buydate2: this.dataForm.buydate2,
          company: this.dataForm.company,
          usecompany: this.dataForm.usecompany,
          ntype: this.dataForm.ntype,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.records;
          this.totalPage = data.page.pages;
          this.totalCount = data.page.total;
          this.currentCount = data.page.records.length;
          this.typeTree = data.trees;
          if (this.sum <= 1) {
            this.sum++;
            this.dataList.forEach((d) => {
              this.$set(d, "checked", false);
            });
            this.typeTree.forEach((t) => {
              this.expandAll(t);
            });
          }
          this.dialogVisible = false;
          this.searchVisible = false;
          
          // this.clearDataForm()
          // if (isAdd) {
          //   this.$nextTick(() => {
          //     this.$refs.tcarTable.bodyWrapper.scrollTop =
          //     this.$refs.tcarTable.bodyWrapper.scrollHeight;
          //   });
          // }
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
        this.searchVisible = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandle(val) {
      if (val.length > 0) {
        this.dataList.forEach((d) => {
          d.checked = true;
          this.$refs.tcarTable.toggleRowSelection(d, true);
        });
      } else {
        this.dataList.forEach((d) => {
          d.checked = false;
        });
        this.$refs.tcarTable.clearSelection();
      }
      this.dataListSelections = val;
    },
    // 关闭车辆档案弹窗
    dialogToClose() {
      this.dialogVisible = false;
      this.getDataList();
    },
    // 新增 / 修改
    addOrUpdateHandle(row) {
      this.clearFormData();
      this.addOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(row, this.isUpdate);
        this.isUpdate = false;
      });
    },
    // 删除
    deleteHandle(id) {
      var ids = id
        ? [id]
        : this.dataListSelections.map((item) => {
            return item.id;
          });
      this.$confirm(
        `确定对[id=${ids.join(",")}]进行[${id ? "删除" : "批量删除"}]操作?`,
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning",
        }
      )
        .then(() => {
          this.$http({
            url: this.$http.adornUrl("/generator/tcarcar/delete"),
            method: "post",
            data: this.$http.adornData(ids, false),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              if (this.dataList.length < 1) {
                this.pageIndex = 1;
              }
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.getDataList();
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          });
        })
        .catch((_) => {});
    },
    handleClose(done) {
      this.$confirm("确认关闭？")
        .then((_) => {
          done();
        })
        .catch((_) => {});
    },
    // 高级查询
    onSearch() {
      this.searchVisible = true;
    },
    // 取消选中
    cancelSelected() {
      var selected = this.dataList.filter((d) => d.checked == true);
      selected.forEach((s) => {
        s.checked = false;
      });
    },
    handleNodeClick(val, node) {
      if (val.parentId != 100 || val.parentId != 0) {
        this.clearFormData();
        switch (node.parent.label) {
          case "按注册单位":
            this.dataForm.company = val.label;
            break;
          case "按车辆类型":
            this.dataForm.type = val.label;
            break;
          case "按使用单位":
            this.dataForm.usecompany = val.label;
            break;
          default:
            this.clearFormData();
            break;
        }
        this.getDataList(true);
      }
    },
    // 树节点展开
    handleNodeExpand(data) {
      // 如果数组没有该id则保存当前展开的节点
      let isAdd = this.nodeIds.find((item) => item == data.deptid);
      // this.$nextTick(() => {
      //   this.$refs.tree.store.nodesMap[data.deptid].expanded = true;
      //   this.controllMap(this.$refs.tree.store.nodesMap[data.deptid], true);
      // });
      if (!isAdd) {
        this.nodeIds.push(data.deptid); // 在节点展开时添加到默认展开数组
        // this.removeChildrenIds(data,false)
      }
    },
    // 树节点关闭
    handleNodeCollapse(data) {
      // this.$nextTick(() => {
      //   this.$refs.tree.store.nodesMap[data.deptid].expanded = false;
      //   this.controllMap(this.$refs.tree.store.nodesMap[data.deptid], false);
      // });
      this.nodeIds = this.nodeIds.filter((item) => item != data.deptid); // 收起时过滤数组对应选项
      this.removeChildrenIds(data, true); // 这里主要针对多级树状结构，当关闭父节点时，递归删除父节点下的所有子节点
    },
    controllMap(data, flag) {
      const that = this;
      this.$nextTick(() => {
        if (data.childNodes) {
          data.childNodes.forEach((c) => {
            c.expanded = flag;
            that.controllMap(c, flag);
          });
        }
      });
    },
    // 移除树节点id数组的子节点id
    removeChildrenIds(data, flag) {
      const that = this;
      if (data.children) {
        data.children.forEach((item) => {
          const index = that.nodeIds.includes(item.deptid);
          if (flag) {
            if (index > 0) {
              that.nodeIds.splice(index, 1);
              that.removeChildrenIds(item, flag);
            }
          }
          if (index < 0) {
            that.nodeIds.push(item.deptid);
            that.removeChildrenIds(item, flag);
          }
        });
      }
    },
    padZero(...args) {
      args[1] = args[1] < 10 ? "0" + args[1] : args[1];
      args[2] = args[2] < 10 ? "0" + args[2] : args[2];
      args[0][1] = args[1];
      args[0][2] = args[2];
      return Number.parseInt(args[0].join(""));
    },
    clearFormData() {
      for (let key in this.dataForm) {
        // 清空属性值
        delete this.dataForm[key];
      }
    },
    uniq(array) {
      let arrNum = array.reduce((prev, cur) => {
        if (!prev.includes(cur)) {
          prev.push(cur);
        }
        return prev;
      }, []);
      return arrNum;
    },
    getTypetree() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/generator/tcarcar/typelist"),
        method: "get",
      }).then(({ data }) => {
        if (data && data.code === 0) {
          console.log(data);
          this.typeTree = [];
          var list = data.list;
          for (let i in list) {
            this.typeTree.push(list[i]);
          }
        } else {
          this.typeTree = [];
        }
        this.dataListLoading = false;
      });
    },
    getDistanceMonth(startTime, endTime) {
      startTime = new Date(startTime);
      endTime = new Date(endTime);
      var dateToMonth = 0;
      var startDate =
        startTime.getDate() +
        startTime.getHours() / 24 +
        startTime.getMinutes() / 24 / 60;
      var endDate =
        endTime.getDate() +
        endTime.getHours() / 24 +
        endTime.getMinutes() / 24 / 60;
      if (endDate >= startDate) {
        dateToMonth = 0;
      } else {
        dateToMonth = -1;
      }
      let yearToMonth = (endTime.getFullYear() - startTime.getFullYear()) * 12;
      let monthToMonth = endTime.getMonth() - startTime.getMonth();
      return yearToMonth + monthToMonth + dateToMonth;
    },
    // 默认展开所有节点
    expandAll(data) {
      const that = this;
      if (!this.nodeIds.find((n) => n == data.deptid)) {
        that.nodeIds.push(data.deptid);
      }
      this.expandChild(data);
    },
    expandChild(data) {
      const that = this;
      if (data.children) {
        data.children.forEach((n) => {
          // this.$refs.tree.store.nodesMap[n.deptid].expanded = true
          if (!that.nodeIds.find((item) => item == n.deptid)) {
            that.nodeIds.push(n.deptid);
          }
          that.expandChild(n);
        });
      }
    },
    clearDataForm() {
      for (let i in this.dataForm) {
        delete this.dataForm[i];
      }
    },
  },
  computed: {
    // 计算选中的个数
    selectComputed() {
      const selected = this.dataList.filter((d) => d.checked == true);
      this.dataListSelections = selected;
      if (this.dataListSelections.length == this.currentCount) {
        this.dataList.forEach((d) => {
          this.$refs.tcarTable.toggleRowSelection(d, true);
        });
      } else {
        this.$refs.tcarTable.clearSelection();
      }
      return selected;
    },
    // 计算保险到期时间是否还有3个月，如果还有3个月用红色字体高亮显示
    checkInsurance() {
      return (row) => {
        if (row.insurance && row.insurance.bxdate && row.insurance.expiredate) {
          let month = this.getDistanceMonth(
            row.insurance.bxdate,
            row.insurance.expiredate
          );
          if (month == 3) {
            return {
              color: "red",
            };
          }
        }
      };
    },
    // 计算年检到期时间是否还有3个月，如果还有3个月用红色字体高亮显示
    checkInspection() {
      return (row) => {
        if (
          row.inspection &&
          row.inspection.njdate &&
          row.inspection.expiredate
        ) {
          let month = this.getDistanceMonth(
            row.inspection.njdate,
            row.inspection.expiredate
          );
          if (month == 3) {
            return {
              color: "red",
            };
          }
        }
      };
    },
  },
  watch: {
    "dataForm.type": {
      handler(newVal, oldVal) {
        if (newVal && newVal == "小轿车") {
          this.dataForm.ntype = 1;
        } else if (newVal && newVal == "大货车") {
          this.dataForm.ntype = 2;
        }  else {
          this.dataForm.ntype = 0;
        }
      },
    },
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

.el-aside {
  background-color: #d3dce6;
  color: #333;
  text-align: center;
  line-height: 50px;
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
</style>