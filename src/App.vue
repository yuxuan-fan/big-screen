<template>
  <div class="screen-container">
    <dv-full-screen-container class="screen-bg">

      <div class="header">
        <dv-border-box-8>
          <h1 class="title">电子标牌大屏</h1>
        </dv-border-box-8>
      </div>

      <div class="top-cards">
        <div class="card-item" v-for="(item, index) in topCards" :key="index">
          <dv-border-box-1>
            <div class="card-content">
              <div class="card-value">{{ item.value }}</div>
              <div class="card-label">{{ item.label }}</div>
            </div>
          </dv-border-box-1>
        </div>
      </div>

      <div class="main-body">
        <div class="left-panel">
          <dv-border-box-2>
            <div class="panel-title">疫苗种类占比</div>
            <div ref="warningChart" class="chart-box"></div>
          </dv-border-box-2>
        </div>

        <div class="center-panel">
          <div class="center-top">
            <dv-border-box-2>
              <div class="panel-title">各型号标牌数量</div>
              <div ref="usageChart" class="chart-box"></div>
            </dv-border-box-2>
          </div>
          <div class="center-bottom">
            <dv-border-box-2>
              <div class="panel-title">效期预警分布</div>
              <div ref="rankChart" class="chart-box"></div>
            </dv-border-box-2>
          </div>
        </div>

        <div class="right-panel">
          <dv-border-box-2>
            <div class="panel-title">标牌状态</div>
            <dv-scroll-board :config="scrollConfig" class="scroll-board" />
          </dv-border-box-2>
        </div>
      </div>

    </dv-full-screen-container>
  </div>
</template>

<script>
import * as echarts from 'echarts'

export default {
  name: 'App',
  data() {
    return {
      topCards: [
        { label: '在线标牌', value: 48 },
        { label: '离线标牌', value: 2 },
        { label: '本月预警', value: 5 },
        { label: '标牌总数', value: 326 }
      ],
      scrollConfig: {
        header: ['序号', '疫苗名称', '状态', '电量'],
        data: [
          ['01', '乙肝疫苗', '在线', '85%'],
          ['02', '脊灰疫苗', '在线', '72%'],
          ['03', '麻腮风疫苗', '离线', '--'],
          ['04', '百白破疫苗', '在线', '90%'],
          ['05', '流脑疫苗', '在线', '65%'],
          ['06', '乙脑疫苗', '在线', '78%'],
          ['07', '甲肝疫苗', '在线', '88%'],
          ['08', '水痘疫苗', '在线', '92%']
        ],
        oddRowBGC: 'rgba(0, 212, 255, 0.05)',
        evenRowBGC: 'rgba(0, 212, 255, 0.02)',
        headerBGC: '#0a1628'
      }
    }
  },
  mounted() {
    setTimeout(() => {
      this.initWarningChart()
      this.initRankChart()
      this.initUsageChart()
      this.removeBorderAnimation()
    }, 500)
    window.addEventListener('resize', () => {
      this.resizeCharts()
    })
  },
  methods: {
    removeBorderAnimation() {
      const animates = document.querySelectorAll('animate, animateTransform, animateMotion')
      animates.forEach(a => a.remove())
      document.querySelectorAll('.dv-border-box-8 svg').forEach(s => s.style.display = 'none')
      document.querySelectorAll('.dv-border-box-1 svg').forEach(s => s.style.display = 'none')
    },
    initWarningChart() {
      this.warningChart = echarts.init(this.$refs.warningChart)
      this.warningChart.setOption({
        backgroundColor: 'transparent',
        series: [{
          type: 'pie',
          radius: ['40%', '70%'],
          center: ['50%', '50%'],
          data: [
            { value: 75, name: '乙肝疫苗', itemStyle: { color: '#00d4ff' } },
            { value: 60, name: '脊灰疫苗', itemStyle: { color: '#6bcb77' } },
            { value: 52, name: '麻腮风疫苗', itemStyle: { color: '#ffd93d' } },
            { value: 45, name: '百白破疫苗', itemStyle: { color: '#ff6b6b' } },
            { value: 40, name: '乙脑疫苗', itemStyle: { color: '#ff8e53' } },
            { value: 35, name: '流脑疫苗', itemStyle: { color: '#a78bfa' } },
            { value: 32, name: '水痘疫苗', itemStyle: { color: '#f472b6' } },
            { value: 28, name: '甲肝疫苗', itemStyle: { color: '#34d399' } }
          ],
          label: { color: '#fff', formatter: '{b}\n{d}%' }
        }]
      })
    },
    initRankChart() {
      const days = []
      for (let i = 29; i >= 0; i--) {
        const d = new Date()
        d.setDate(d.getDate() - i)
        days.push((d.getMonth() + 1) + '/' + d.getDate())
      }
      this.rankChart = echarts.init(this.$refs.rankChart)
      this.rankChart.setOption({
        backgroundColor: 'transparent',
        grid: { left: '3%', right: '5%', top: '10%', bottom: '10%', containLabel: true },
        xAxis: {
          type: 'category',
          data: days,
          axisLine: { lineStyle: { color: '#1e3a5f' } },
          axisLabel: { color: '#8899aa', rotate: 45, interval: 4 },
          axisTick: { show: false }
        },
        yAxis: {
          type: 'value',
          axisLine: { lineStyle: { color: '#1e3a5f' } },
          axisLabel: { color: '#8899aa' },
          splitLine: { lineStyle: { color: '#1e3a5f' } }
        },
        series: [{
          type: 'line',
          data: [25, 28, 26, 27, 29, 26, 28, 25, 27, 30, 28, 26, 29, 27, 28, 25, 26, 29, 27, 28, 26, 25, 27, 28, 26, 29, 27, 28, 26, 27],
          smooth: true,
          symbol: 'circle',
          symbolSize: 6,
          lineStyle: { color: '#00d4ff', width: 2 },
          itemStyle: { color: '#00d4ff' },
          areaStyle: {
            color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
              { offset: 0, color: 'rgba(0, 212, 255, 0.3)' },
              { offset: 1, color: 'rgba(0, 212, 255, 0.02)' }
            ])
          }
        }]
      })
    },
    initUsageChart() {
      this.usageChart = echarts.init(this.$refs.usageChart)
      this.usageChart.setOption({
        backgroundColor: 'transparent',
        grid: { left: '3%', right: '10%', top: '10%', bottom: '5%', containLabel: true },
        xAxis: {
          type: 'category',
          data: ['2.66寸三色', '2.9寸三色', '2.8寸四色', '4.2寸'],
          axisLine: { lineStyle: { color: '#1e3a5f' } },
          axisLabel: { color: '#fff' }
        },
        yAxis: {
          type: 'value',
          axisLine: { lineStyle: { color: '#1e3a5f' } },
          axisLabel: { color: '#8899aa' },
          splitLine: { lineStyle: { color: '#1e3a5f' } }
        },
        series: [{
          type: 'bar',
          data: [120, 85, 65, 56],
          itemStyle: {
            color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
              { offset: 0, color: '#00d4ff' },
              { offset: 1, color: '#0066ff' }
            ])
          },
          barWidth: 40
        }]
      })
    },
    initRankChart() {
      this.rankChart = echarts.init(this.$refs.rankChart)
      this.rankChart.setOption({
        backgroundColor: 'transparent',
        series: [{
          type: 'pie',
          radius: ['40%', '70%'],
          center: ['50%', '50%'],
          data: [
            { value: 75, name: '正常', itemStyle: { color: '#6bcb77' } },
            { value: 20, name: '次月预警', itemStyle: { color: '#ffd93d' } },
            { value: 5, name: '本月预警', itemStyle: { color: '#ff6b6b' } }
          ],
          label: { color: '#fff', formatter: '{b}\n{d}%' }
        }]
      })
    },
    resizeCharts() {
      this.warningChart && this.warningChart.resize()
      this.rankChart && this.rankChart.resize()
      this.usageChart && this.usageChart.resize()
    }
  }
}
</script>

<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body { background: #0a1628; overflow: hidden; }
.screen-container { width: 100vw; height: 100vh; }
.screen-bg { background: linear-gradient(180deg, #0a1628 0%, #0d1f3c 100%); }

.header { height: 80px; display: flex; align-items: center; justify-content: center; }
.title { font-size: 32px; color: #00d4ff; letter-spacing: 8px; text-shadow: 0 0 20px rgba(0, 212, 255, 0.6); padding: 15px 60px; }

.top-cards { display: flex; justify-content: space-around; padding: 10px 30px; }
.card-item { flex: 1; margin: 0 10px; height: 80px; }
.card-content { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100%; }
.card-value { font-size: 36px; font-weight: bold; color: #00d4ff; }
.card-item:nth-child(2) .card-value { color: #ff6b6b; }
.card-item:nth-child(3) .card-value { color: #ffd93d; }
.card-item:nth-child(4) .card-value { color: #6bcb77; }
.card-label { font-size: 14px; color: #8899aa; margin-top: 5px; }

.main-body { display: flex; padding: 15px 30px; height: calc(100vh - 200px); gap: 15px; }
.left-panel, .right-panel { flex: 1; overflow: hidden; }
.center-panel { flex: 1.5; display: flex; flex-direction: column; gap: 15px; overflow: hidden; }
.center-top, .center-bottom { flex: 1; overflow: hidden; }
.panel-title { font-size: 16px; color: #00d4ff; padding: 15px 20px 10px; border-bottom: 1px solid rgba(30, 58, 95, 0.5); }
.chart-box { width: 100%; height: calc(100% - 50px); padding: 10px; overflow: hidden; }
.scroll-board { height: calc(100% - 50px); padding: 10px; overflow: hidden; }

/* 自定义科技感边框 */
.dv-border-box-1 {
  position: relative;
}
.dv-border-box-1::before {
  content: '';
  position: absolute;
  top: 0; left: 0;
  width: 20px; height: 20px;
  border-top: 2px solid #4fd2dd;
  border-left: 2px solid #4fd2dd;
}
.dv-border-box-1::after {
  content: '';
  position: absolute;
  bottom: 0; right: 0;
  width: 20px; height: 20px;
  border-bottom: 2px solid #4fd2dd;
  border-right: 2px solid #4fd2dd;
}
.dv-border-box-1 > .border-box-content {
  position: relative;
}
.dv-border-box-1 > .border-box-content::before {
  content: '';
  position: absolute;
  top: 0; right: 0;
  width: 20px; height: 20px;
  border-top: 2px solid #4fd2dd;
  border-right: 2px solid #4fd2dd;
}
.dv-border-box-1 > .border-box-content::after {
  content: '';
  position: absolute;
  bottom: 0; left: 0;
  width: 20px; height: 20px;
  border-bottom: 2px solid #4fd2dd;
  border-left: 2px solid #4fd2dd;
}
</style>
