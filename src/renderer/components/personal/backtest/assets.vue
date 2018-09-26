<template>
  <div class='main_content'>

    <div>
      <h2 align='left'>> 业绩表现</h2>
    </div>
    <div class='container'>

      <router-link :to="{name:'history',id:this.data0}">
        <mu-raised-button label='成交明细' class='demo-raised-button'/>
      </router-link>
      <mu-raised-button v-on:click='ready()' label='账户表现' class='demo-raised-button'  primary/>
      <router-link :to="{name:'strategy',id:this.data0}">
        <mu-raised-button label='策略查看' class='demo-raised-button' />
      </router-link>
      <mu-raised-button label='说明' class='demo-raised-button' @click='showToast' />
      <mu-toast v-if='toast' message='此版本仅适用于1.1.7.dev2 ++的回测演示' @close='hideToast' />
      <mu-divider />
    </div>
    <div>
      <mu-table>

        <mu-th>alpha</mu-th>
        <mu-th>beta</mu-th>
        <mu-th>sharpe</mu-th>
        <mu-th>最大回撤</mu-th>

        <template v-for='item in items'>
          <mu-tbody>

            <mu-td>{{item['alpha']}}</mu-td>
            <mu-td>{{item['beta']}}</mu-td>
            <mu-td>{{item['sharpe']}}</mu-td>
            <mu-td>{{item['max_drop']}}</mu-td>
            
          </mu-tbody>
        </template>
      </mu-table>
      <mu-table>
        <mu-th>持续期</mu-th>
        <mu-th>年化收益</mu-th>
        <mu-th>波动率</mu-th>
        <mu-th>Benchmark年化收益</mu-th>

        <template v-for='item in items'>
          <mu-tbody>
            <mu-td>{{item['exist']}}</mu-td>
            <mu-td>{{item['annualized_returns']}}</mu-td>
            <mu-td>{{item['vol']}}</mu-td>
            <mu-td>{{item['benchmark_annualized_returns']}}</mu-td>

          </mu-tbody>
        </template>
      </mu-table>

    </div>
    <div>
      <h2 align='left'>> 资金曲线</h2>
    </div>
    <div id='main'></div>

    <div>
      <h2 align='left'>> 组合分析</h2>
    </div>
    <div>

    </div>
  </div>
</template>
<script>
import echarts from 'echarts'
// 基于准备好的dom，初始化echarts实例
import axios from 'axios'
// 绘制图表
export default {
  data () {
    return {
      chart: null,
      data0: this.$route.params.id,
      time: [],
      items: [
        {
          alpha: 'loading..'
        }
      ],
      toast: false
    }
  },
  methods: {
    drawline (id) {
      this.chart = echarts.init(document.getElementById(id))

      this.chart.setOption({
        title: {
          text: this.data0
        },
        grid: {
          x: '5%',
          y: '15%',
          // x2:300,
          // y2:400,
          borderWidth: 1
        },
        toolbox: {
          show: true,
          feature: {
            dataZoom: {
              yAxisIndex: 'none'
            },
            dataView: {
              readOnly: false
            },
            magicType: {
              type: ['line', 'bar']
            },
            restore: {},
            saveAsImage: {}
          }
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          }
        },
        xAxis: [
          {
            data: [],
            scale: true
          },
          {
            data: [],
            scale: true
            // boundaryGap : false,
            // axisLine: {onZero: true}
          }
        ],
        yAxis: [
          {},
          {
            name: 'account',
            max: 'dataMax',
            min: 'dataMin'
          }
        ],
        legend: {
          data: [
            {
              name: 'assets'
            },
            {
              name: 'benchmark'
            }
          ],
          // data:['k_line'],
          x: 'left',
          top: '5%'
        },
        dataZoom: [
          {
            show: true,
            realtime: true,
            start: 0,
            end: 100
          }
        ],
        series: [
          {
            name: 'assets',
            type: 'line',
            data: [],
            lineStyle: {
              normal: {
                color: '#c23531'
              }
            },
            areaStyle: {
              normal: {
                color: '#c23531',
                opacity: 0.3
              }
            },
            yAxisIndex: 1
          },
          {
            name: 'benchmark',
            type: 'line',
            data: [],
            lineStyle: {
              normal: {
                color: '#2f4554'
              }
            },
            areaStyle: {
              normal: {
                color: '#2f4554',
                opacity: 0.3
              }
            },
            yAxisIndex: 1
          }
        ]
      })
    },
    ready () {
      // 先从ready拿到信息数据
      let val = this.data0
      axios
        .get('http://localhost:8010/risk?account_cookie=' + val)
        .then(response => {
          var data = response.data['result'][0]

          // var profit = data['total_returns']

          this.items[0]['alpha'] = data['alpha'].toFixed(3)
          this.items[0]['beta'] = data['beta'].toFixed(4)
          this.items[0]['max_drop'] = data['max_dropback'].toFixed(3)
          this.items[0]['sharpe'] = data['sharpe'].toFixed(3)
          this.items[0]['vol'] = data['volatility'].toFixed(5)
          this.items[0]['annualized_returns'] = data['annualize_return'].toFixed(3)
          this.items[0]['benchmark_annualized_returns'] = data['bm_annualizereturn'].toFixed(3)
          this.items[0]['exist'] = data['time_gap']

          this.chart.setOption({
            title: {
              text: data['strategy']
            }
          })
          var benchmarkhistory = data['benchmark_assets']
          var assets = data['assets']
          var totaltimeindex = data['totaltimeindex']
          this.chart.setOption({
            series: [
              {
                name: 'benchmark',
                type: 'line',
                data: benchmarkhistory,
                lineStyle: {
                  normal: {
                    color: '#2f4554'
                  }
                },
                areaStyle: {
                  normal: {
                    color: '#2f4554',
                    opacity: 0.3
                  }
                }
              },
              {
                name: 'assets',
                type: 'line',
                data: assets,
                lineStyle: {
                  normal: {
                    color: '#c23531'
                  }
                },
                areaStyle: {
                  normal: {
                    color: '#c23531',
                    opacity: 0.3
                  }
                }
              }
            ],
            xAxis: {
              data: totaltimeindex,
              zlevel: 1,
              gridIndex: 0
            }
          })
        })
    },
    showToast () {
      this.toast = true
      if (this.toastTimer) clearTimeout(this.toastTimer)
      this.toastTimer = setTimeout(() => {
        this.toast = false
      }, 2000)
    },
    hideToast () {
      this.toast = false
      if (this.toastTimer) clearTimeout(this.toastTimer)
    }
  },

  mounted () {
    this.$nextTick(function () {
      this.drawline('main')
      this.ready()
    })
  }
}
</script>
<style>
#main {
  position: relative;
  left: 0;
  margin-left: 0px;
  margin-bottom: 0px;
  width: 1200px;
  height: 600px;
  border-radius: 10px;
}

#but {
  width: 100px;
  height: 50px;
}

.container {
  display: flex;
  flex-wrap: wrap;
}
</style>
