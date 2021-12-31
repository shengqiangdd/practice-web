<template>
  <el-dialog
    title="高级查询"
    :close-on-click-modal="false"
    :visible.sync="visible"
    @close="toClose">
    <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="toSearch()"  label-width="100px">
    <el-form-item label="车牌：" prop="number">
        <el-col :span="11">
            <el-input v-model.trim="dataForm.number" placeholder="车牌"></el-input>
        </el-col>
    </el-form-item>
    <el-form-item label="车辆品牌：" prop="brand">
        <el-col :span="11">
            <el-input v-model.trim="dataForm.brand" placeholder="车辆品牌"></el-input>
        </el-col>
    </el-form-item>
    <el-form-item label="车型：" prop="model">
        <el-col :span="11">
            <el-input v-model.trim="dataForm.model" placeholder="车型"></el-input>
        </el-col>
    </el-form-item>
    <el-form-item label="载客人数:" prop="seating">
        <el-col :span="11">
            <el-input type="number" v-model.number="dataForm.seating" placeholder="载客人数"></el-input>
        </el-col>
    </el-form-item>
    <el-form-item label="购置日期:" prop="buydate">
        <el-date-picker
            v-model="dataForm.buydate1"
            type="date"
            align="right"
            placeholder="">
        </el-date-picker>
        &nbsp;&nbsp;至：
        <el-date-picker
            v-model="dataForm.buydate2"
            type="date"
            align="right"
            placeholder="">
        </el-date-picker>
    </el-form-item>
    <el-form-item label="注册单位：" prop="company">
        <el-col :span="11">
            <el-input v-model.trim="dataForm.company" placeholder="注册单位"></el-input>
        </el-col>
    </el-form-item>
    <el-form-item label="使用单位：" prop="usecompany">
        <el-col :span="11">
            <el-input v-model.trim="dataForm.usecompany" placeholder="使用单位"></el-input>
        </el-col>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="toClose">取消</el-button>
      <el-button type="primary" @click="toSearch">查询</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    props: ['dataForm'],
    data () {
      return {
        visible: true
      }
    },
    methods: {
        toSearch () {
            this.visible = false
            this.$emit('dataList')
            this.$emit('toCloseSearch')
        },
        toClose() {
            this.visible = false
            this.$emit('toCloseSearch')
        }
    }
  }
</script>
