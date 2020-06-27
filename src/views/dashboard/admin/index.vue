<template>
  <div class="dashboard-editor-container">
    <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:32px;">
      <TimelineChart :chart-data="mean_timelineChartData" :panel-title="rawDataPanelTitle" />
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
      now: +new Date(),
      rawDataPanelTitle: '原始数据',
      kurtPanelTitle: '峭度',
      skewnessPanelTitle: '偏斜度',
      peakPanelTitle: '峰值指标',
      intensityPanelTitle: '振动烈度'
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
        this.kurt_timelineChartData.push([this.now, ioip_data.kurt_value])
        this.peak_timelineChartData.push([this.now, ioip_data.peak_value])
        this.skewness_timelineChartData.push([this.now, ioip_data.skewness_value])
        this.intensity_timelineChartData.push([this.now, ioip_data.intensity_value])

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

}
</style>
