<template>
  <div class="dashboard-editor-container">
    <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
      <TimelineChart :chart-data="mean_timelineChartData" />
    </el-row>

  </div>
</template>

<script>
import TimelineChart from './components/TimelineChart'

const lineChartData = {
  newVisitis: {
    expectedData: [100, 120, 161, 134, 105, 160, 165],
    actualData: [120, 82, 91, 154, 162, 140, 145]
  },
  messages: {
    expectedData: [200, 192, 120, 144, 160, 130, 140],
    actualData: [180, 160, 151, 106, 145, 150, 130]
  },
  purchases: {
    expectedData: [80, 100, 121, 104, 105, 90, 100],
    actualData: [120, 90, 100, 138, 142, 130, 130]
  },
  shoppings: {
    expectedData: [130, 140, 141, 142, 145, 150, 160],
    actualData: [120, 82, 91, 154, 162, 140, 130]
  }
}

export default {
  name: 'DashboardAdmin',
  components: {
    TimelineChart
  },
  data() {
    return {
      lineChartData: lineChartData.newVisitis,
      raw_timelineChartData: [],
      mean_timelineChartData: [],
      now: +new Date()
    }
  },
  mounted: function() {
    setInterval(() => {
      window.eel.get_ioip_data(this.now - 1000, this.now)((ioip_data) => {
        console.log(ioip_data)
        var ad_data = ioip_data.ad_data
        var total = 0
        for (var i = 0; i < ad_data.length; i++) {
          total = total + ad_data[i][1]
          this.raw_timelineChartData.push(ad_data[i])
        }

        var mean_value = total / ad_data.length

        this.mean_timelineChartData.push([this.now, mean_value])
        if (ad_data.length > 0) {
          this.now = this.raw_timelineChartData[this.raw_timelineChartData.length - 1][0] + 1000
        }
      })
    }, 1000)
  },
  methods: {
    handleSetLineChartData(type) {
      this.lineChartData = lineChartData[type]
    }
  }
}
</script>

<style lang="scss" scoped>
.dashboard-editor-container {
  padding: 32px;
  background-color: rgb(240, 242, 245);
  position: relative;

  .chart-wrapper {
    background: #fff;
    padding: 16px 16px 0;
    margin-bottom: 32px;
  }
}

@media (max-width:1024px) {
  .chart-wrapper {
    padding: 8px;
  }
}
</style>
