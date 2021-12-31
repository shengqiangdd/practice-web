<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList(dataForm.id)">
      <el-form-item style="margin-top:10px;margin-left:10px">
        <el-button @click="getDataList(dataForm.id)">刷新</el-button>
        <el-button :disabled="disableBtn" v-if="isAuth('generator:tcardriver:save')" type="primary" @click="addOrUpdateHandle(dataForm.id, isSelect)">新增</el-button>
        <el-button v-if="isAuth('generator:tcardriver:delete')" type="danger" @click="deleteHandle()" :disabled="selectComputed.length <= 0">删除选中</el-button>
        <el-button type="danger" @click="cancelSelected()" :disabled="selectComputed.length <= 0">取消选中</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      ref="elTable"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
        <template slot-scope="scope">
          <el-checkbox v-model="scope.row.checked"></el-checkbox>
        </template>
      </el-table-column>
      <el-table-column
        prop="name"
        header-align="center"
        align="center"
        label="姓名">
      </el-table-column>
      <el-table-column
        prop="sex"
        header-align="center"
        align="center"
        label="性别">
        <template slot-scope="scope">
          {{ sexComputed(scope.row.sex) }}
        </template>
      </el-table-column>
      <el-table-column
        prop="tel"
        header-align="center"
        align="center"
        label="手机">
      </el-table-column>
      <el-table-column
        prop="type"
        header-align="center"
        align="center"
        label="准驾车型">
      </el-table-column>
      <el-table-column
        prop="remark"
        header-align="center"
        align="center"
        label="备注">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id, isAdd=false)">修改</el-button>
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
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
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList(dataForm.id)"></add-or-update>

    <!-- 司机选择 -->
    <select-driver v-if="selctDriverDialog" ref="selectDriver"  @tCarDriverAdd="addDriver" @refreshDataList="getDataList(dataForm.id)"></select-driver>
  </div>
</template>

<script>
import SelectDriver from './selectDriver.vue'
  import AddOrUpdate from './tcardriver-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          id: 0
        },
        isAdd: true,
        isSelect: false,
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        addOrUpdateVisible: false,
        selctDriverDialog: false
      }
    },
    components: {
      AddOrUpdate,
      SelectDriver
    },
    mounted() {
  
    },
    activated () {
      this.getDataList()
    },
    methods: {
      init (id, isAdd) {
        if(id != null || isAdd) {
          this.isSelect = true
        }
        this.dataForm.id = id || 0
        this.$nextTick(() => {
          this.getDataList(this.dataForm.id)
        })
      },
      // 获取数据列表
      getDataList (id) {
        this.isAdd = true
        var num = 0
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl(`/generator/tcardriver/list`),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'cid': id
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.records
            this.totalPage = data.page.pages
            if(num < 2) {
              num++
            }
            if(num == 1) {
              this.dataList.forEach(d => { this.$set(d,'checked',false) })
            }
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.getDataList(this.dataForm.id)
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList(this.dataForm.id)
      },
      // 多选
      selectionChangeHandle (val) {
        if(val.length > 0) {
          this.dataList.forEach(d => {
            d.checked = true
            this.$refs.elTable.toggleRowSelection(d, true)
          })
        } else {
          this.dataList.forEach(d => {
            d.checked = false
          })
          this.$refs.elTable.clearSelection()
        }
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id, isSelect) {
        if(id && isSelect) {
          this.selctDriverDialog = true
          this.$nextTick(() => {
            this.$refs.selectDriver.init('t_car_driver')
          })
        } else {
          this.addOrUpdateVisible = true
          this.$nextTick(() => {
            this.$refs.addOrUpdate.init(id, this.isAdd)
          })
        }
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/generator/tcardriver/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList(this.dataForm.id)
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
        .catch(_ => {})
      },
      // 取消选中
      cancelSelected() {
        var selected = this.dataList.filter(d => d.checked == true)
        selected.forEach(s => { s.checked = false })
      },
      // 新增或者修改选择了取消
      closeDialog() {
        this.isAdd = true
      },
      // 把当前车辆的id传过去，为选中的司机添加绑定到当前车辆的id
      addDriver() {
        this.$refs.selectDriver.selectToAdd(this.dataForm.id)
      }
    },
    computed: {
      // 计算选中的个数
      selectComputed() {
        const selected = this.dataList.filter(d => d.checked == true)
        const nocheck = this.dataList.filter(d => d.checked == false)
        this.dataListSelections = selected
        if(this.dataListSelections.length == this.totalPage) {
            this.dataList.forEach(d => {
              this.$refs.elTable.toggleRowSelection(d, true)
            })
        } else {
            this.$refs.elTable.clearSelection()
            // nocheck.forEach(n => {
            //   this.$refs.elTable.toggleRowSelection(n, false)
            // })
        }
        return selected
      },
      // 当司机数量大于0时，给按钮添加禁用属性
      disableBtn() {
        if(this.dataList.length > 0) {
          return true
        }
      },
      sexComputed() {
        return (val) => {
          if (val === 1) {
            return '男'
          } else {
            return '女'
          }
        }
      }
    },
    watch: {
      // dataListSelections: {
      //   handler(newVal, oldNal) {
      //     console.log(newVal.length,oldNal.length)
      //     if(newVal.length == this.totalPage) {
            
      //     } else {
            
      //     }
      //   }
      // }
    }
  }
</script>
<style scoped>
  .mod-config{
    box-shadow: 0 2px 4px rgba(0, 0, 0, .12), 0 0 6px rgba(0, 0, 0, .04)
  }
</style>
