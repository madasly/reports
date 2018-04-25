<template>
    <el-dialog
  :visible.sync="show"
  center>
  <span class="price" v-if="((this.stepStatus === 'fixed') || (this.stepStatus === 'closed'))">Reward: {{report.reward}} €</span>
  <h1>Report {{report.id}}: {{report.title}}</h1>
  <h2> severity level: {{report.severity}}</h2>
  <h3>reported by: {{report.author.username}} ({{report.displayDate}})</h3>
  <p>{{report.description}}</p>
  <p>{{report.poc}}</p>
  <span slot="footer" class="dialog-footer">
    <div class="amount" v-if="this.stepStatus === 'rewarded'">
    <el-form :model="report" ref="report">
      <el-form-item props="amountReward">
        <el-input v-model="report.reward" style="width: 20%;" placeholder="Amount of reward"></el-input>
</el-form-item>
    </el-form>
  </div>
    <el-button type="danger" @click="cancelEdit()">Cancel</el-button>
    <el-button type="success" @click="changeStatus()">pass to {{stepStatus}}</el-button>
  </span>
</el-dialog>
  </template>
<script>
import axios from 'axios'
export default {
  name: '',
  props: ['report', 'show'],
  data: () => ({
    centerDialogVisible: false,
    stepStatus: '',
    urlServer: 'http://localhost:3000/reports/'
  }),
  watch: {
    show: function () {
      if (this.show) this.changeConfirmButton()
    }
  },
  methods: {
    changeConfirmButton () {
      switch (this.report.status) {
        case 'new':
          this.stepStatus = 'accepted'
          break
        case 'accepted':
          this.stepStatus = 'rewarded'
          break
        case 'rewarded':
          this.stepStatus = 'fixed'
          break
        case 'fixed':
          this.stepStatus = 'closed'
          break
      }
    },
    cancelEdit () {
      this.$emit('close')
    },
    changeStatus (report) {
      console.log(this.stepStatus)

      this.url = this.urlServer + this.report.id
      this.report.status = this.stepStatus
      if (this.stepStatus === 'rewarded') {
        axios.patch(this.url, { 'status': this.report.status, 'reward': this.report.reward }).then((response) => {
          this.$emit('close')
        })
      } else {
        axios.patch(this.url, { 'status': this.report.status }).then((response) => {
          this.$emit('close')
          console.log(response.data)
        })
      }
    }
  }
}
</script>
<style scoped>
h1{
  color: black;
}
h2{
  color: red;
}
h3{
  color: maroon;
}
p{
  color: blue;
  font-size: 20px;
}
p:last-child{
  font-size: 20px;
  line-height: 35px;
  color: #252e52;
}
.price{
  float: right;
  color: green;
  font-size: 30px;
  font-weight: bold;
}
.amount{
  margin-bottom: 20px;
}
</style>
