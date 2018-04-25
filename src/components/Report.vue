<template>
<div class="main">
  <modal :show="showModal" :report="selectedReport" @close="deselect"></modal>
  <h1>My dashboard</h1>
  <div class="container-fluid">
    <el-row :gutter="20">
      <el-col :span="6">
        <div class="cell-review">
          <div class="bar"></div>
          <h2>Reports to review</h2>
          <div class="item" v-for="report in newReports" @click="selectReport(report)">
            <p>{{report.title}}<span class="circle"></span>
            </p>
            <p>{{report.bugbounty.name}}</p><br>
            <p class="details">{{report.author.username}}</p>
            <p class="details">{{report.displayDate}}</p>
          </div>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="cell-pay">
          <div class="bar2"></div>
          <h2>Reports to pay</h2>
          <div class="item" v-for="report in acceptedReports" @click="selectReport(report)">
            <p>{{report.title}}<span class="circle"></span></p>
            <p>{{report.bugbounty.name}}</p><br>
            <p class="details">{{report.author.username}}</p>
            <p class="details">{{report.displayDate}}</p>
          </div>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="cell-fix">
          <div class="bar3"></div>
          <h2>Reports to fix</h2>
          <div class="item" v-for="report in rewardedReports" @click="selectReport(report)">
            <p>{{report.title}}<span class="circle"></span></p>
            <p>{{report.bugbounty.name}}</p><br>
            <p class="details">{{report.author.username}}</p>
            <p class="details">{{report.displayDate}}</p>
          </div>
        </div>
      </el-col>
      <el-col :span="6">
        <div class="cell-close">
          <div class="bar4"></div>
          <h2>Reports to close</h2>
          <div class="item" v-for="report in fixedReports" @click="selectReport(report)">
            <p>{{report.title}}<span class="circle"></span></p>
            <p>{{report.bugbounty.name}}</p><br>
            <p class="details">{{report.author.username}}</p>
            <p class="details">{{report.displayDate}}</p>
          </div>
        </div>
      </el-col>
    </el-row>
  </div>
</div>
</template>

<script>
import axios from 'axios'
import Modal from '@/components/Modal'
import moment from 'moment'
export default {
  components: {
    Modal
  },
  name: 'HelloWorld',
  data () {
    return {
      showModal: false,
      selectedReport: undefined,
      url: 'http://localhost:3000/reports/',
      status: '',
      reports: [],
      newReports: [],
      acceptedReports: [],
      rewardedReports: [],
      fixedReports: [],
      closedReports: []
    }
  },
  watch: {
    showModal () {
      this.getReports()
    }
  },
  methods: {
    selectReport (report) {
      this.selectedReport = report
      this.showModal = true
    },
    deselect () {
      this.selectedItem = undefined
      this.showModal = false
    },
    getReports () {
      axios.get(this.url)
        .then((res) => {
          this.reports = res.data
          for (var i = 0; i < this.reports.length; i++) {
            this.reports[i].displayDate = moment(this.reports[i].reported_at).locale('fr').format('llll')
          }
          this.newReports = res.data.filter(report => report.status === 'new')
          this.acceptedReports = res.data.filter(report => report.status === 'accepted')
          this.rewardedReports = res.data.filter(report => report.status === 'rewarded')
          this.fixedReports = res.data.filter(report => report.status === 'fixed')
          this.closedReports = res.data.filter(report => report.status === 'closed')
          console.log(this.reports)
          console.log('Report Closed:', this.closedReports)
        })
    }
  },
  mounted () {
    this.getReports()
  }
}
</script>

<style scoped>
body {
  margin: 0;
  padding: 0;
  font-family: Arial, sans-serif;
}

h1 {
  text-transform: uppercase;
  text-align: left;
  padding: 0 75px;
  font-weight: normal;
}

.clear {
  clear: both;
}

.cell-review,
.cell-pay,
.cell-fix,
.cell-close {
  width: 90%;
  height: 750px;
  max-height: 1200px;
  margin: 0 auto;
  padding: 10px;
  border-radius: 15px;
  border: 1px solid #ccc;
}

.cell-review {
  border-top: 5px solid orange;
}

.cell-pay {
  border-top: 5px solid red;
}

.cell-fix {
  border-top: 5px solid #d93250;
}

.cell-close {
  border-top: 5px solid black;
}

h2 {
  text-align: left;
  font-weight: 200;
  margin-bottom: 40px;
  padding-left: 25px;
}

.item {
  float: left;
  width: 90%;
  font-size: 18px;
  line-height: 8px;
  border: 1px solid #ccc;
  border-radius: 15px;
  text-align: left;
  padding: 10px 8px;
  margin: 20px 0;
  color: black;
}

.item:hover {
  cursor: pointer;
}

.bar {
  border-left: 12px solid #eee;
  border-radius: 15px;
  height: 600px;
  position: absolute;
  left: 22.6%;
  top: 16%;
}

.bar2 {
  border-left: 12px solid #eee;
  border-radius: 15px;
  height: 600px;
  position: absolute;
  left: 47.6%;
  top: 16%;
}

.bar3 {
  border-left: 12px solid #eee;
  border-radius: 15px;
  height: 600px;
  position: absolute;
  right: 26.7%;
  top: 16%;
}

.bar4 {
  border-left: 12px solid #eee;
  border-radius: 15px;
  height: 600px;
  position: absolute;
  right: 1.8%;
  top: 16%;
}

.circle {
  float: right;
  width: 18px;
  height: 18px;
  border-radius: 50%;
  background-color: cyan;
}

.details {
  font-weight: bold;
  color: #bbb;
}
</style>
