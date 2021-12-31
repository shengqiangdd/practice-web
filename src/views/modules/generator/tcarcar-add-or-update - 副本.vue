<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '车辆信息修改'"
    :close-on-click-modal="false"
    :visible.sync="visible"
    width="60%">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-row>
        <el-col :span="8">
          <div class="grid-content">
            <el-form-item label="车牌" prop="number">
              <el-input v-model="dataForm.number" placeholder="车牌"></el-input>
            </el-form-item>
            <el-form-item label="车辆类型">
              <el-select v-model="dataForm.type" placeholder="">
                <el-option
                  v-for="item in carTypes"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
             </el-select>
          </el-form-item>
          <el-form-item label="载客人数" prop="seating">
      <el-input type="number" v-model="dataForm.seating" placeholder="载客人数"></el-input>
    </el-form-item>
    <el-form-item label-width="120px" label="保养里程(公里)" prop="servicemileage">
      <el-input type="number" v-model="dataForm.servicemileage" placeholder="保养里程(公里)"></el-input>
    </el-form-item>
    <el-form-item label="车架号" prop="framenumber">
      <el-input v-model="dataForm.framenumber" placeholder="车架号"></el-input>
    </el-form-item>
    <el-form-item label="购置时间" prop="buydate">
      <el-date-picker
      v-model="dataForm.buydate"
      type="date"
      align="right"
      placeholder="购置时间">
      </el-date-picker>
    </el-form-item>
    <el-form-item label="是否停用" prop="isstop">
      <el-radio v-model="dataForm.isstop" label="1">是</el-radio>
      <el-radio v-model="dataForm.isstop" label="0">否</el-radio>
    </el-form-item>
          </div>
        </el-col>
        <el-col :span="8">
          <div class="grid-content">
            <el-form-item label="车辆品牌" prop="brand">
              <el-input v-model="dataForm.brand" placeholder="车辆品牌"></el-input>
            </el-form-item>
            <el-form-item label="车辆颜色" prop="color">
      <el-input v-model="dataForm.color" placeholder="车辆颜色"></el-input>
    </el-form-item>
    <el-form-item label-width="120px" label="油耗(L/百公里)" prop="fuelconsumption">
      <el-input type="number" v-model="dataForm.fuelconsumption" placeholder="油耗(L/百公里)"></el-input>
    </el-form-item>
    <el-form-item label="保养周期" prop="servicecycle">
      <el-input v-model="dataForm.servicecycle" placeholder="保养周期"></el-input>
    </el-form-item>
    <el-form-item label-width="120px" label="供应商(4S店)" prop="vendor">
      <el-input v-model="dataForm.vendor" placeholder="供应商"></el-input>
    </el-form-item>
    <el-form-item label="注册单位" prop="company">
      <el-input v-model="dataForm.company" placeholder="注册单位"></el-input>
    </el-form-item>
          </div>
        </el-col>
        <el-col :span="8">
          <div class="grid-content">
            <el-form-item label="车型" prop="model">
              <el-input v-model="dataForm.model" placeholder="车型"></el-input>
            </el-form-item>
            <el-form-item label="载重(吨)" prop="load">
      <el-input type="number" v-model="dataForm.load" placeholder="载重(吨)"></el-input>
    </el-form-item>
    <el-form-item label-width="120px" label="初始里程(公里)" prop="initmileage">
      <el-input type="number" v-model="dataForm.initmileage" placeholder="初始里程(公里)"></el-input>
    </el-form-item>
    <el-form-item label="发动机号" prop="enginenumber">
      <el-input v-model="dataForm.enginenumber" placeholder="发动机号"></el-input>
    </el-form-item>
    <el-form-item label-width="120px" label="购入价格(元)" prop="buyprice">
      <el-input type="number" v-model="dataForm.buyprice" placeholder="购入价格(元)"></el-input>
    </el-form-item>
    <el-form-item label="使用单位" prop="usecompany">
      <el-input v-model="dataForm.usecompany" placeholder="使用单位"></el-input>
    </el-form-item>
          </div>
        </el-col>
    </el-row>
  <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="onDialogClosed">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          number: '',
          brand: '',
          model: '',
          type: '',
          color: '',
          load: '',
          seating: '',
          fuelconsumption: '',
          initmileage: '',
          servicemileage: '',
          servicecycle: '',
          enginenumber: '',
          framenumber: '',
          vendor: '',
          buyprice: '',
          buydate: '',
          company: '',
          deptid: '',
          deptidName: '',
          usecompany: '',
          usedept: '',
          status: '',
          remark: '',
          isstop: ''
        },
        carTypes: [{
          value: 1,
          label: '小轿车'
        }, {
          value: 2,
          label: '大客车'
        }],
        dataRule: {
          number: [
            { required: true, message: '车牌不能为空', trigger: 'blur' }
          ],
          brand: [
            { required: true, message: '车辆品牌不能为空', trigger: 'blur' }
          ],
          model: [
            { required: true, message: '车型不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '车辆类型不能为空', trigger: 'blur' }
          ],
          color: [
            { required: true, message: '车辆颜色不能为空', trigger: 'blur' }
          ],
          load: [
            { required: true, message: '载重(吨)不能为空', trigger: 'blur' }
          ],
          seating: [
            { required: true, message: '载客人数不能为空', trigger: 'blur' }
          ],
          fuelconsumption: [
            { required: true, message: '油耗(L/百公里)不能为空', trigger: 'blur' }
          ],
          initmileage: [
            { required: true, message: '初始里程(公里)不能为空', trigger: 'blur' }
          ],
          servicemileage: [
            { required: true, message: '保养里程(公里)不能为空', trigger: 'blur' }
          ],
          servicecycle: [
            { required: true, message: '保养周期不能为空', trigger: 'blur' }
          ],
          enginenumber: [
            { required: true, message: '发动机号不能为空', trigger: 'blur' }
          ],
          framenumber: [
            { required: true, message: '车架号不能为空', trigger: 'blur' }
          ],
          vendor: [
            { required: true, message: '供应商不能为空', trigger: 'blur' }
          ],
          buyprice: [
            { required: true, message: '购入价格(元)不能为空', trigger: 'blur' }
          ],
          buydate: [
            { required: true, message: '购置时间不能为空', trigger: 'blur' }
          ],
          company: [
            { required: true, message: '注册单位不能为空', trigger: 'blur' }
          ],
          deptid: [
            { required: true, message: '所属部门不能为空', trigger: 'blur' }
          ],
          deptidName: [
            { required: true, message: '部门不能为空', trigger: 'blur' }
          ],
          usecompany: [
            { required: true, message: '使用单位不能为空', trigger: 'blur' }
          ],
          usedept: [
            { required: true, message: '使用部门不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '当前状态不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '备注不能为空', trigger: 'blur' }
          ],
          isstop: [
            { required: true, message: '是否停用不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/generator/tcarcar/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.number = data.tCarCar.number
                this.dataForm.brand = data.tCarCar.brand
                this.dataForm.model = data.tCarCar.model
                this.dataForm.type = data.tCarCar.type
                this.dataForm.color = data.tCarCar.color
                this.dataForm.load = data.tCarCar.load
                this.dataForm.seating = data.tCarCar.seating
                this.dataForm.fuelconsumption = data.tCarCar.fuelconsumption
                this.dataForm.initmileage = data.tCarCar.initmileage
                this.dataForm.servicemileage = data.tCarCar.servicemileage
                this.dataForm.servicecycle = data.tCarCar.servicecycle
                this.dataForm.enginenumber = data.tCarCar.enginenumber
                this.dataForm.framenumber = data.tCarCar.framenumber
                this.dataForm.vendor = data.tCarCar.vendor
                this.dataForm.buyprice = data.tCarCar.buyprice
                this.dataForm.buydate = data.tCarCar.buydate
                this.dataForm.company = data.tCarCar.company
                this.dataForm.deptid = data.tCarCar.deptid
                this.dataForm.deptidName = data.tCarCar.deptidName
                this.dataForm.usecompany = data.tCarCar.usecompany
                this.dataForm.usedept = data.tCarCar.usedept
                this.dataForm.status = data.tCarCar.status
                this.dataForm.remark = data.tCarCar.remark
                this.dataForm.isstop = data.tCarCar.isstop
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/generator/tcarcar/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'number': this.dataForm.number,
                'brand': this.dataForm.brand,
                'model': this.dataForm.model,
                'type': this.dataForm.type,
                'color': this.dataForm.color,
                'load': this.dataForm.load,
                'seating': this.dataForm.seating,
                'fuelconsumption': this.dataForm.fuelconsumption,
                'initmileage': this.dataForm.initmileage,
                'servicemileage': this.dataForm.servicemileage,
                'servicecycle': this.dataForm.servicecycle,
                'enginenumber': this.dataForm.enginenumber,
                'framenumber': this.dataForm.framenumber,
                'vendor': this.dataForm.vendor,
                'buyprice': this.dataForm.buyprice,
                'buydate': this.dataForm.buydate,
                'company': this.dataForm.company,
                'deptid': this.dataForm.deptid,
                'deptidName': this.dataForm.deptidName,
                'usecompany': this.dataForm.usecompany,
                'usedept': this.dataForm.usedept,
                'status': this.dataForm.status,
                'remark': this.dataForm.remark,
                'isstop': this.dataForm.isstop
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      },
      // 取消修改
      onDialogClosed() {
        this.visible = false
        this.$emit('tCarUpdateClosed')
      }
    }
  }
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
</style>
