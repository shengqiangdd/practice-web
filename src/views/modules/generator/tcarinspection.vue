<template>
  <div class="mod-config">
    <el-form
      :inline="true"
      :model="dataForm"
      @keyup.enter.native="getDataList(dataForm.id)"
    >
      <el-form-item>
        <el-button
          v-if="isAuth('generator:tcarinspection:save')"
          type="primary"
          @click="addOrUpdateHandle(dataForm.id)"
          >新增</el-button
        >
        <el-button
          v-if="isAuth('generator:tcarinsurance:save')"
          type="success"
          @click="addOrUpdateHandle(upId)"
          >编辑</el-button
        >
        <el-button @click="getDataList(dataForm.id)">刷新</el-button>
        <el-button
          v-if="isAuth('generator:tcarinspection:delete')"
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
      @selection-change="selectionChangeHandle"
      ref="inspectionTable"
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
        label="年检号"
      >
      </el-table-column>
      <el-table-column
        prop="njdate"
        header-align="center"
        align="center"
        label="年检日期"
      >
      </el-table-column>
      <el-table-column
        prop="njcost"
        header-align="center"
        align="center"
        label="年检费用"
      >
      </el-table-column>
      <el-table-column
        prop="adminoffice"
        header-align="center"
        align="center"
        label="车管所"
      >
      </el-table-column>
      <el-table-column
        prop="expiredate"
        header-align="center"
        align="center"
        label="到期日期"
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
      @closeDialog="closeDialog"
      @refreshDataList="getDataList(dataForm.id)"
    ></add-or-update>
  </div>
</template>

<script>
import AddOrUpdate from "./tcarinspection-add-or-update";
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
      addOrUpdateVisible: false,
      upId: "",
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
    init(id, isUpdate) {
      this.dataForm.id = id || 0;
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
        url: this.$http.adornUrl(`/generator/tcarinspection/list`),
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
          this.$refs.inspectionTable.toggleRowSelection(d, true);
        });
      } else {
        this.dataList.forEach((d) => {
          d.checked = false;
        });
        this.$refs.inspectionTable.clearSelection();
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
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id, this.isAdd);
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
          url: this.$http.adornUrl("/generator/tcarinspection/delete"),
          method: "post",
          data: this.$http.adornData(ids, false),
        }).then(({ data }) => {
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
        });
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
          this.$refs.inspectionTable.toggleRowSelection(d, true);
        });
      } else {
        this.$refs.inspectionTable.clearSelection();
      }
      return selected;
    },
  },
};
</script>
