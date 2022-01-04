<template>
  <div class="mod-config">
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList(dataForm.id)"
    >
      <el-form-item>
        <el-button
          v-if="isAuth('generator:tcarinsurance:save')"
          type="success"
          @click="addOrUpdateHandle(dataForm.id)"
          >新增</el-button
        >
        <el-button
          v-if="isAuth('generator:tcarinsurance:save')"
          type="success"
          :disabled="upId.length <= 0"
          @click="addOrUpdateHandle(upId,isAdd = false)"
          >编辑</el-button
        >
        <el-button @click="getDataList(dataForm.id)">刷新</el-button>
        <el-button
          v-if="isAuth('generator:tcarinsurance:delete')"
          type="danger"
          @click="deleteHandle()"
          :disabled="selectComputed.length <= 0"
          >删除选中</el-button
        >
        <el-button
          type="danger"
          @click="cancelSelected()"
          :disabled="selectComputed.length <= 0"
          >取消选中</el-button
        >
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      ref="insuranceTable"
      @selection-change="selectionChangeHandle"
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
        prop="number"
        header-align="center"
        align="center"
        label="保单号"
      >
      </el-table-column>
      <el-table-column
        prop="bxdate"
        header-align="center"
        align="center"
        label="保险时间"
      >
      </el-table-column>
      <el-table-column
        prop="bxtype"
        header-align="center"
        align="center"
        label="保险种类"
      >
      </el-table-column>
      <el-table-column
        prop="amount"
        header-align="center"
        align="center"
        label="保险金额"
      >
      </el-table-column>
      <el-table-column
        prop="company"
        header-align="center"
        align="center"
        label="保险公司"
      >
      </el-table-column>
      <el-table-column
        prop="expiredate"
        header-align="center"
        align="center"
        label="到期时间"
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
            @click="addOrUpdateHandle(scope.row.id, (isAdd = false))"
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
      @refreshDataList="getDataList(dataForm.id)"
    ></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from "./tcarinsurance-add-or-update";
export default {
  data() {
    return {
      dataForm: {
        id: 0,
      },
      isAdd: true,
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      upId: "",
      addOrUpdateVisible: false,
      currentCarRow: {},
    };
  },
  components: {
    AddOrUpdate,
  },
  mounted() {},
  activated() {
    this.getDataList();
  },
  methods: {
    init(row, isUpdate) {
      this.dataForm.id = row.id || 0;
      this.currentCarRow = row || {};
      this.$nextTick(() => {
        this.getDataList(this.dataForm.id);
      });
    },
    // 获取数据列表
    getDataList(id) {
      this.isAdd = true;
      var num = 0;
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl(`/generator/tcarinsurance/list`),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
          cid: id,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.list;
          this.totalPage = data.page.totalCount;
          if (num < 2) {
            num++;
          }
          if (num == 1) {
            this.dataList.forEach((d) => {
              this.$set(d, "checked", false);
            });
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
      this.pageIndex = 1;
      this.getDataList(this.dataForm.id);
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList(this.dataForm.id);
    },
    // 多选
    selectionChangeHandle(val) {
      if (val.length > 0) {
        this.dataList.forEach((d) => {
          d.checked = true;
          this.$refs.insuranceTable.toggleRowSelection(d, true);
        });
      } else {
        this.dataList.forEach((d) => {
          d.checked = false;
        });
        this.$refs.insuranceTable.clearSelection();
      }
      this.dataListSelections = val;
    },
    // 新增 / 修改
    addOrUpdateHandle(id) {
      if (id && id.length > 1) {
        this.$message({
          message: "请只选择一个值",
          type: "error",
        });
      } else {
        this.addOrUpdateVisible = true;
        id = id ? (typeof(id) == 'number' ? id : id[0]) : 0
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id, this.isAdd, this.currentCarRow);
        });
      }
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
          url: this.$http.adornUrl("/generator/tcarinsurance/delete"),
          method: "post",
          data: this.$http.adornData(ids, false),
        })
          .then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                  this.getDataList(this.dataForm.id);
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          })
          .catch((_) => {});
      });
    },
    // 新增或者修改选择了取消
    closeDialog() {
      this.isAdd = true;
    },
    // 取消选中
    cancelSelected() {
      var selected = this.dataList.filter((d) => d.checked == true);
      selected.forEach((s) => {
        s.checked = false;
      });
    },
  },
  computed: {
    // 计算选中的个数
    selectComputed() {
      const selected = this.dataList.filter((d) => d.checked == true);
      const nocheck = this.dataList.filter((d) => d.checked == false);
      this.dataListSelections = selected;
      this.upId = selected.map((item) => {
        return item.id;
      });
      if (this.dataListSelections.length == this.totalPage) {
        this.dataList.forEach((d) => {
          this.$refs.insuranceTable.toggleRowSelection(d, true);
        });
      } else {
        this.$refs.insuranceTable.clearSelection();
      }
      return selected;
    },
  },
};
</script>
