<template>
  <!-- 选择司机弹窗 -->
  <el-dialog
    title="选择司机"
    :close-on-click-modal="false"
    :visible.sync="visible"
    :append-to-body="true"
    :before-close="handleClose"
  >
    <el-select v-model="selectValue" placeholder="请选择">
      <el-option
        v-for="item in dataList"
        :key="item.id"
        :label="item.name"
        :value="item.id"
      >
        <span style="float: left">{{ item.id }}</span>
        <span style="float: right; color: #8492a6; font-size: 13px">{{
          item.name
        }}</span>
      </el-option>
    </el-select>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取 消</el-button>
      <el-button type="primary" @click="dateSelected">确 定</el-button>
    </span>
  </el-dialog>
</template>
<script>
export default {
  data() {
    return {
      visible: true,
      selectValue: "",
      dataList: [],
      dataListLoading: false,
      pageIndex: 1,
      pageSize: 10,
      atName: ''
    };
  },
  mounted() {},
  methods: {
    init(name) {
      this.visible = true
      this.atName = name || undefined
      this.getDataList();
    },
    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      this.$http({
        url: this.$http.adornUrl(`/generator/tcardriver/list`),
        method: "get",
        params: this.$http.adornParams({
          page: this.pageIndex,
          limit: this.pageSize,
        }),
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.page.records;
        } else {
          this.dataList = [];
        }
        this.dataListLoading = false;
      });
    },
    handleClose() {},
    // 选择数据
    dateSelected() {
        this.visible = false;
        if(this.atName == 't_car_driver') {
          this.$emit('tCarDriverAdd')
        } else if(this.atName == 't_car_run') {
          const selected = this.dataList.filter(d => that.selectValue == d.id)
          this.$emit('tCarRunDriverAdd', selected)
        }
    },
    // 把选中的司机的id改为传过来的id，将选中的司机绑定到id上
    selectToAdd(id) {
        const that = this
        const selected = this.dataList.filter(d => that.selectValue == d.id)[0]
        this.$http({
            url: this.$http.adornUrl(
              `/generator/tcardriver/save`
            ),
            method: "post",
            data: this.$http.adornData({
              id: id || undefined,
              name: selected.name,
              sex: selected.sex,
              tel: selected.tel,
              type: selected.type,
              remark: selected.remark,
            }),
          }).then(({ data }) => {
            if (data && data.code === 0) {
              this.$message({
                message: "操作成功",
                type: "success",
                duration: 1500,
                onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                },
              });
            } else {
              this.$message.error(data.msg);
            }
          })
          .catch(_=> {})
    }
  },
};
</script>
