<template>
  <div class="dashboard-editor-container">
    <el-row style="background:#fff;padding:16px 16px 0;margin-bottom:5px;">
      <TimelineChart :chart-data="mean_timelineChartData" :panel-title="rawDataPanelTitle" :now="now" />
    </el-row>

    <el-row style="background:#fff;padding:0px 0px 0;margin-bottom:5px;">
      <LineChart :chart-data="fft_lineChartData" :panel-title="fftPanelTitle" />
    </el-row>

    <el-row style="background:#fff" :gutter="32">
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="kurt_timelineChartData" :panel-title="kurtPanelTitle" :now="now" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="skewness_timelineChartData" :panel-title="skewnessPanelTitle" :now="now" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="peak_timelineChartData" :panel-title="peakPanelTitle" :now="now" />
        </div>
      </el-col>
      <el-col :xs="24" :sm="24" :lg="12">
        <div class="chart-wrapper">
          <TimelineChart :chart-data="intensity_timelineChartData" :panel-title="intensityPanelTitle" :now="now" />
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import TimelineChart from './components/TimelineChart'
import LineChart from './components/LineChart'

export default {
  name: 'DashboardAdmin',
  components: {
    TimelineChart,
    LineChart
  },
  data() {
    return {
      raw_timelineChartData: [],
      mean_timelineChartData: [],
      kurt_timelineChartData: [],
      peak_timelineChartData: [],
      skewness_timelineChartData: [],
      intensity_timelineChartData: [],
      fft_lineChartData: [],
      now: +new Date(),
      rawDataPanelTitle: '原始数据',
      kurtPanelTitle: '峭度',
      skewnessPanelTitle: '偏斜度',
      peakPanelTitle: '峰值指标',
      intensityPanelTitle: '振动烈度',
      fftPanelTitle: '频谱数据'
    }
  },
  mounted: function() {
    setInterval(() => {
      window.eel.get_ioip_data(this.now - 1000, this.now)((ioip_data) => {
        var ad_data = ioip_data.ad_data

        var total = 0
        for (var i = 0; i < ad_data.length; i++) {
          total = total + ad_data[i][1]
        }
        var mean_value = total / ad_data.length

        this.mean_timelineChartData.push([this.now, mean_value])
        this.kurt_timelineChartData.push([this.now, ioip_data.kurt_value[0][1]])
        this.peak_timelineChartData.push([this.now, ioip_data.peak_value[0][1]])
        this.skewness_timelineChartData.push([this.now, ioip_data.skewness_value[0][1]])
        this.intensity_timelineChartData.push([this.now, ioip_data.intensity_value[0][1]])

        for (i = 0; i < ioip_data.fft_value.length; i++) {
          this.fft_lineChartData.push(ioip_data.fft_value[i])
        }
        this.keepLastElements(this.fft_lineChartData, ioip_data.fft_value.length)
        console.log(this.fft_lineChartData)

        var n = 1000
        this.keepLastElements(this.mean_timelineChartData, n)
        this.keepLastElements(this.kurt_timelineChartData, n)
        this.keepLastElements(this.peak_timelineChartData, n)
        this.keepLastElements(this.skewness_timelineChartData, n)
        this.keepLastElements(this.intensity_timelineChartData, n)

        if (ad_data.length > 0) {
          this.now = ad_data[ad_data.length - 1][0] + 1000
        }
      })
    }, 1000)
  },
  methods: {
    keepLastElements(arr, n) {
      arr.splice(0, arr.length - n)
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
