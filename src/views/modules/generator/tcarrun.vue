<template>
  <el-container>
    <el-header>派车台账</el-header>
    <el-container>
      <el-aside width="200px">
        <div class="typetree">
          <el-tree
            node-key="id"
            :data="typeTree"
            :default-expanded-keys="nodeIds"
            :props="defaultProps"
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
            @keyup.enter.native="getDataList(true)"
          >
            <el-form-item label="">
              <el-date-picker
                v-model="dataForm.begintime"
                type="date"
                align="right"
                placeholder=""
                style="width: 100%"
                value-format="yyyy-MM-dd"
              >
              </el-date-picker>
            </el-form-item>
            <span class="dateto">至：</span>

            <el-form-item label="">
              <el-date-picker
                v-model="dataForm.endtime"
                type="date"
                align="right"
                placeholder=""
                style="width: 100%"
                value-format="yyyy-MM-dd"
              >
              </el-date-picker>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="getDataList(true)"
                >查询</el-button
              >
            </el-form-item>
          </el-form>
          <el-form :inline="true">
            <el-form-item>
              <el-button @click="getDataList()">刷新</el-button>
              <el-button
                v-if="isAuth('generator:tcarrun:save')"
                type="primary"
                @click="addOrUpdateHandle()"
                >新增</el-button
              >
              <el-button
                v-if="isAuth('generator:tcarrun:delete')"
                type="danger"
                @click="deleteHandle()"
                :disabled="dataListSelections.length <= 0"
                >删除选中</el-button
              >
              <el-button
                type="danger"
                @click="cancelSelected"
                :disabled="dataListSelections.length <= 0"
                >取消选中</el-button
              >
            </el-form-item>
          </el-form>
          <el-table
            :data="dataList"
            ref="tCarRunTable"
            border
            v-loading="dataListLoading"
            @selection-change="selectionChangeHandle"
            style="width: 100%"
          >
            <el-table-column
              type="selection"
              header-align="center"
              align="center"
              width="50"
            >
            </el-table-column>
            <el-table-column
              prop="tcarNum"
              header-align="center"
              align="center"
              label="车号"
            >
            </el-table-column>
            <el-table-column
              prop="begintime"
              header-align="center"
              align="center"
              label="开始时间"
            >
            </el-table-column>
            <el-table-column
              prop="endtime"
              header-align="center"
              align="center"
              label="结束时间"
            >
            </el-table-column>
            <el-table-column
              prop="beginplace"
              header-align="center"
              align="center"
              label="起始地"
            >
            </el-table-column>
            <el-table-column
              prop="endplace"
              header-align="center"
              align="center"
              label="目的地"
            >
            </el-table-column>
            <el-table-column
              prop="driver"
              header-align="center"
              align="center"
              label="司机"
            >
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
                  @click="addOrUpdateHandle(scope.row)"
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
            :total="totalPage"
            layout="total, sizes, prev, pager, next, jumper"
          >
          </el-pagination>
          <!-- 弹窗, 新增 / 修改 -->
          <add-or-update
            v-if="addOrUpdateVisible"
            ref="addOrUpdate"
            @refreshDataList="getDataList"
          ></add-or-update>
        </div>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
import AddOrUpdate from "./tcarrun-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        begintime: null,
        endtime: null,
        carid: "",
        deptid: "",
        treeMonth: null,
        carnum: "",
        depname: "",
      },
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      addOrUpdateVisible: false,
      nodeIds: [],
      typeTree: [
        {
          label: "所有记录",
        },
      ],
      defaultProps: {
        children: "children",
        label: "label",
      },
    };
  },
  components: {
    AddOrUpdate,
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
      this.dataListLoading = true;
      var num = 0;
      this.$http({
        url: this.$http.adornUrl("/generator/tcarrun/list"),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          begintime: this.dataForm.begintime,
          endtime: this.dataForm.endtime,
          carid: this.dataForm.carid,
          treeMonth: this.dataForm.treeMonth,
          carnum: this.dataForm.carnum,
          depname: this.dataForm.depname,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          console.log(data);
          this.dataList = data.page.records;
          this.totalPage = data.page.total;
          if (num < 2) {
            num++;
          }
          if (num == 1) {
            this.getTypetree();
          }
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
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
      this.dataListSelections = val;
    },
    // 新增 / 修改
    addOrUpdateHandle(row) {
      this.addOrUpdateVisible = true;
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(row);
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
      ).then(() => {
        this.$http({
          url: this.$http.adornUrl("/generator/tcarrun/delete"),
          method: "post",
          data: this.$http.adornData(ids, false),
        })
          .then(({ data }) => {
            if (data && data.code === 0) {
              if (this.dataList.length < 1) {
                this.pageIndex--;
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
          })
          .catch((_) => {});
      });
    },
    // 取消选中
    cancelSelected() {
      var selected = this.dataListSelections;
      // 让选中的每行数据取消选中状态，设置为false
      selected.forEach((s) => {
        this.$refs.tCarRunTable.toggleRowSelection(s, false);
      });
      selected.splice(0, selected.length);
    },
    handleNodeClick(val, node) {
      var year = "";
      var month = "";
      const parent = node.parent;
      this.clearFormData();
      var year = "";
      var month = "";
      if (parent.parent != null) {
        switch (parent.parent.label) {
          case "按使用单位":
            this.dataForm.carnum = val.label;
            break;
          case "按时间":
            year = parent.data.key;
            month = val.key;
            this.dataForm.treeMonth = year + "-" + month + "-" + "01";
            break;
          default:
            this.clearFormData();
            break;
        }
        this.getDataList(true);
        this.clearFormData();
      } else {
        if (val.label === "所有记录") {
          this.clearFormData();
          this.getDataList(true);
        }
      }
    },
    // 节点展开时
    handleNodeExpand(data) {
      let isAdd = this.nodeIds.find((item) => item == data.id);
      // 如果数组没有该id则保存当前展开的节点
      if (!isAdd) {
        this.nodeIds.push(data.id); // 在节点展开时添加到默认展开数组
      }
    },
    // 树节点关闭
    handleNodeCollapse(data) {
      console.log(data);
      this.nodeIds = this.nodeIds.filter((item) => item != data.id); // 收起时过滤数组对应选项
      this.removeChildrenIds(data) // 这里主要针对多级树状结构，当关闭父节点时，递归删除父节点下的所有子节点
    },
    // 删除树子节点
    removeChildrenIds(data) {
      const that = this
      if (data.children) {
        data.children.forEach(item => {
          const index = that.nodeIds.indexOf(item.id)
          if (index > 0) {
            that.nodeIds.splice(index, 1)
          }
          that.removeChildrenIds(item)
        })
      }
    },
    clearFormData() {
      for (let key in this.dataForm) {
        // 清空属性值
        delete this.dataForm[key];
      }
    },
    getTypetree() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl("/generator/tcarrun/typelist"),
        method: "get",
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.typeTree = [];
          var all = { id: 1, label: "所有记录", children: [] };
          var list = data.list;
          for (let i in list) {
            if (list[i].label === "按使用单位") {
              all.children.push(list[i]);
              this.typeTree.push(all);
            } else {
              this.typeTree.push(list[i]);
            }
          }
        } else {
          this.typeTree = [];
        }
        this.dataListLoading = false;
      });
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

.dateto {
  line-height: 36px;
}
</style>
