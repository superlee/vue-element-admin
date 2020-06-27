<template>
  <div class="dashboard-editor-container">
    <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
      <TimelineChart :chart-data="raw_timelineChartData" :panel-title="rawDataPanelTitle" />
    </el-row>
    <el-row style="background:#fff" :gutter="32">
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="kurt_timelineChartData" :panel-title="kurtPanelTitle" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="skewness_timelineChartData" :panel-title="skewnessPanelTitle" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="peak_timelineChartData" :panel-title="peakPanelTitle" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="intensity_timelineChartData" :panel-title="intensityPanelTitle" />
        </div>
      </el-col>
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
      kurt_timelineChartData: [],
      peak_timelineChartData: [],
      skewness_timelineChartData: [],
      intensity_timelineChartData: [],
      now: +new Date() - 1000,
      rawDataPanelTitle: '原始数据',
      kurtPanelTitle: '峭度',
      skewnessPanelTitle: '偏斜度',
      peakPanelTitle: '峰值指标',
      intensityPanelTitle: '振动烈度'
    }
  },
  mounted: function() {
    setInterval(() => {
      this.now = this.now + 1000
      window.eel.get_ioip_data(this.now - 1000, this.now)((ioip_data) => {
        var ad_data = ioip_data.ad_data
        var total = 0
        for (var i = 0; i < ad_data.length; i++) {
          total = total + ad_data[i][1]
          this.raw_timelineChartData.push(ad_data[i])
          if (this.raw_timelineChartData.length >= 6000) {
            // 100 per second, 60 second
            this.raw_timelineChartData.shift()
          }
        }

        for (i = 0; i < ioip_data.kurt_value.length; i++) {
          this.kurt_timelineChartData.push(ioip_data.kurt_value[i])
          this.peak_timelineChartData.push(ioip_data.peak_value[i])
          this.skewness_timelineChartData.push(ioip_data.skewness_value[i])
          this.intensity_timelineChartData.push(ioip_data.intensity_value[i])
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

}
</style>
