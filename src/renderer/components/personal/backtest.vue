<template>
  <div id="acc">
    <div id="linker">
      <mu-breadcrumb>
        <mu-breadcrumb-item href="/">Personal</mu-breadcrumb-item>
        <mu-breadcrumb-item @click="get_strategymember">Backtest</mu-breadcrumb-item>
      </mu-breadcrumb>
    </div>

    <!-- <div>
        <mu-raised-button @click="get_strategymember">refresh</mu-raised-button>
      </div>
    -->
    <div id="backtest_content">
      <div class="left_side">
        <mu-table
          :height="height"
          :enableSelectAll="enableSelectAll"
          :multiSelectable="multiSelectable"
          :selectable="selectable"
          :showCheckbox="showCheckbox"
          @rowClick="jump"
        >
          <mu-thead slot="header">
            <mu-tr>
              <mu-th>策略</mu-th>
              <mu-th>开始</mu-th>
              <mu-th>结束</mu-th>
              <mu-th>市场</mu-th>
            </mu-tr>
          </mu-thead>
          <template v-for="item in itemDX">
            <mu-tbody v-bind:key="item">
              <mu-tr>
                <mu-td>{{ item[1]}}</mu-td>
                <mu-td>{{ item[2]}}</mu-td>
                <mu-td>{{ item[3]}}</mu-td>
                <mu-td>{{ item[4]}}</mu-td>
              </mu-tr>
            </mu-tbody>
          </template>
        </mu-table>
      </div>

      <div id="viewx">
        <mu-tabs :value="activeTab" @change="handleTabChange">
          <mu-tab value="tab1" @active="getaccountdata" title="交易历史"/>
          <mu-tab value="tab2" @active="getplotdata" title="资金曲线"/>
          <mu-tab value="tab3" @active="getstrategydata" title="策略查看"/>
        </mu-tabs>
        <div v-if="activeTab === 'tab1'">
          <mu-table :height="height" :fixedHeader="true" :showCheckbox="false">
            <mu-thead>
              <mu-tr>
                <mu-td>账户: {{account_cookie}}</mu-td>
                <mu-td>组别: {{acc['portfolio_cookie']}}</mu-td>
                <mu-td>运行时间: {{acc['running_time']}}</mu-td>
              </mu-tr>
              <!-- <mu-tr>
                  <mu-td>账户: {{data0}}</mu-td>
                  <mu-td>组别: {{acc['portfolio_cookie']}}</mu-td>
                  <mu-td>运行时间: {{acc['running_time']}}</mu-td>
              </mu-tr>-->
              <mu-tr>
                <mu-td>日期</mu-td>
                <mu-td>品种</mu-td>
                <mu-td>价格</mu-td>
                <mu-td>买卖数量</mu-td>
                <mu-td>手续费</mu-td>
                <mu-td>方向</mu-td>
              </mu-tr>
            </mu-thead>
            <template v-for="item in items">
              <mu-tbody v-bind:key="item">
                <mu-tr>
                  <mu-td>{{item[0]}}</mu-td>
                  <mu-td>{{item[1]}}</mu-td>
                  <mu-td>{{item[2]}}</mu-td>
                  <mu-td>{{item[3]}}</mu-td>
                  <mu-td>{{item[9]}}</mu-td>
                  <mu-td>{{item[11]}}</mu-td>
                </mu-tr>
              </mu-tbody>
            </template>
          </mu-table>
        </div>
        <div v-if="activeTab === 'tab2'">
          <div>
            <mu-table>
              <mu-th>alpha</mu-th>
              <mu-th>beta</mu-th>
              <mu-th>sharpe</mu-th>
              <mu-th>最大回撤</mu-th>

              <template v-for="item in itemss">
                <mu-tbody v-bind:key="item">
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

              <template v-for="item in itemss">
                <mu-tbody v-bind:key="item">
                  <mu-td>{{item['exist']}}</mu-td>
                  <mu-td>{{item['annualized_returns']}}</mu-td>
                  <mu-td>{{item['vol']}}</mu-td>
                  <mu-td>{{item['benchmark_annualized_returns']}}</mu-td>
                </mu-tbody>
              </template>
            </mu-table>
          </div>
          <div>
            <h2 align="left">> 资金曲线</h2>
          </div>
          <div id="mainxx"></div>
        </div>
        <div v-if="activeTab === 'tab3'">
          <div>
            <h3>{{this.title}}</h3>
            <h4>Last_modified_time:{{this.last_modified_time}}</h4>
          </div>

          <monaco-editor
            class="editor"
            v-model="code"
            theme="vs-dark"
            language="python"
          >{{this.code}}</monaco-editor>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MonacoEditor from "vue-monaco";
import echarts from "echarts";
import axios from "axios";
export default {
  components: {
    MonacoEditor
  },
  data() {
    return {
      chart: null,
      activeTab: "tab1",
      height: "800px",
      selectable: false,
      multiSelectable: false,
      enableSelectAll: false,
      showCheckbox: false,
      user: sessionStorage.user,
      loading: false,
      scroller: null,
      account_cookie: "none",
      acc: { portfolio_cookie: "loading", running_time: "loading" },
      itemDX: [
        {
          user_cookie: "admin",
          portfolio_cookie: "macd_test",
          account_cookie: "future fast",
          strategy: "test_strategy",
          start_time: "2017-12-01",
          end_time: "2018-09-15",
          annualized_returns: "35%"
        }
      ],
      items: [
        [
          "loading",
          "RBL8",
          "loading",
          "多开",
          "loading",
          "xx",
          "xxx",
          "loading"
        ],
        [
          "loading",
          "RBL8",
          "loading",
          "多开",
          "loading",
          "xx",
          "xxx",
          "loading"
        ]
      ],
      time: [],
      itemss: [
        {
          alpha: "loading.."
        }
      ],
      toast: false,
      code: "abc",
      title: "NONE",
      last_modified_time: "none"
    };
  },
  methods: {
    get_strategymember() {
      axios
        .get("http://www.yutiansut.com:8010/accounts/all")
        .then(response => {
          this.itemDX = response.data["result"];
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    jump(index, tr) {
      // console.log(tr)
      this.account_cookie = tr.$children[0].$el.innerText;
      // this.$router.replace({ name: 'assets', params: { id: cookie } })
      if (this.activeTab === "tab1") {
        this.getaccountdata();
      }
      if (this.activeTab === "tab2") {
        this.getplotdata();
      }
      if (this.activeTab === "tab3") {
        this.getstrategydata();
      }
    },
    handleTabChange(val) {
      this.activeTab = val;
    },
    handleActive() {
      window.alert("tab active");
    },
    drawline(id) {
      this.chart = echarts.init(document.getElementById(id));
      this.chart.setOption({
        title: {
          text: this.account_cookie
        },
        grid: {
          x: "5%",
          y: "15%",
          // x2:300,
          // y2:400,
          borderWidth: 1
        },
        toolbox: {
          show: true,
          feature: {
            dataZoom: {
              yAxisIndex: "none"
            },
            dataView: {
              readOnly: false
            },
            magicType: {
              type: ["line", "bar"]
            },
            restore: {},
            saveAsImage: {}
          }
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross"
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
            name: "account",
            max: "dataMax",
            min: "dataMin"
          }
        ],
        legend: {
          data: [
            {
              name: "assets"
            },
            {
              name: "benchmark"
            }
          ],
          // data:['k_line'],
          x: "left",
          top: "5%"
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
            name: "assets",
            type: "line",
            data: [],
            lineStyle: {
              normal: {
                color: "#c23531"
              }
            },
            areaStyle: {
              normal: {
                color: "#c23531",
                opacity: 0.3
              }
            },
            yAxisIndex: 1
          },
          {
            name: "benchmark",
            type: "line",
            data: [],
            lineStyle: {
              normal: {
                color: "#2f4554"
              }
            },
            areaStyle: {
              normal: {
                color: "#2f4554",
                opacity: 0.3
              }
            },
            yAxisIndex: 1
          }
        ]
      });
    },
    showToast() {
      this.toast = true;
      if (this.toastTimer) clearTimeout(this.toastTimer);
      this.toastTimer = setTimeout(() => {
        this.toast = false;
      }, 2000);
    },
    hideToast() {
      this.toast = false;
      if (this.toastTimer) clearTimeout(this.toastTimer);
    },
    getaccountdata() {
      axios
        .get(
          "http://www.yutiansut.com:8010/accounts?account_cookie=" +
            this.account_cookie
        )
        .then(response => {
          this.acc = response.data["result"][0];
          this.items = this.acc["history"];
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    getstrategydata() {
      axios
        .get("http://www.yutiansut.com:8010/strategy/content", {
          params: { account_cookie: this.account_cookie }
        })
        .then(response => {
          var res = response.data["result"][0];
          this.title = res["name"];
          this.code = res["content"];
          this.last_modified_time = res["last_modify_time"];
        });
    },
    getplotdata() {
      this.drawline("mainxx");
      axios
        .get(
          "http://www.yutiansut.com:8010/risk?account_cookie=" +
            this.account_cookie
        )
        .then(response => {
          var data = response.data["result"][0];
          this.itemss[0]["alpha"] = data["alpha"].toFixed(3);
          this.itemss[0]["beta"] = data["beta"].toFixed(4);
          this.itemss[0]["max_drop"] = data["max_dropback"].toFixed(3);
          this.itemss[0]["sharpe"] = data["sharpe"].toFixed(3);
          this.itemss[0]["vol"] = data["volatility"].toFixed(5);
          this.itemss[0]["annualized_returns"] = data[
            "annualize_return"
          ].toFixed(3);
          this.itemss[0]["benchmark_annualized_returns"] = data[
            "bm_annualizereturn"
          ].toFixed(3);
          this.itemss[0]["exist"] = data["time_gap"];
          this.chart.setOption({
            title: {
              text: data["strategy"]
            }
          });
          var benchmarkhistory = data["benchmark_assets"];
          var assets = data["assets"];
          var totaltimeindex = data["totaltimeindex"];
          this.chart.setOption({
            series: [
              {
                name: "benchmark",
                type: "line",
                data: benchmarkhistory,
                lineStyle: {
                  normal: {
                    color: "#2f4554"
                  }
                },
                areaStyle: {
                  normal: {
                    color: "#2f4554",
                    opacity: 0.3
                  }
                }
              },
              {
                name: "assets",
                type: "line",
                data: assets,
                lineStyle: {
                  normal: {
                    color: "#c23531"
                  }
                },
                areaStyle: {
                  normal: {
                    color: "#c23531",
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
          });
        });
    }
  },
  mounted() {
    this.$nextTick(function() {
      this.get_strategymember();
      this.drawline("mainxx");
    });
  },
  watch: {
    // rowClick (index, tr) {
    //   console.log(index)
    //   console.log(tr)
    // },
    $route(to, from) {
      console.log(to);
      console.log(from);
      // this.$router.go(0)
    }
  }
};
</script>


<style lang="css">
#viewx {
  padding-left: 100px;
  width: 1200px;
  height: 1000px;
  display: inline-block;
}
.mu-breadcrumb-item-link,
.mu-breadcrumb-item-current,
.mu-breadcrumb-item-separator {
  font-size: 28px;
}
#linker {
  margin-top: 20px;
}
#backtest_content {
  margin-top: 30px;
}
.mu-item-content {
  font-size: 18px;
  text-align: middle;
  display: inline;
}
.inside_list {
  width: 200px;
  height: 1000px;
  display: block;
}
.left_side {
  width: 600px;
  height: 1000px;
  display: block;
  float: left;
}
.mu-table,
.mu-tr,
.mu-td {
  /* overflow: auto; */
  background-color: rgb(52, 52, 52);
  color: rgb(214, 214, 214);
  overflow-x: hidden;
  overflow-y: hidden;
  scrollbar-face-color: #b90d0d;
  scrollbar-highlight-color: #6c6c90;
  scrollbar-shadow-color: #fcfcfc;
  scrollbar-3dlight-color: #fcfcfc;
  scrollbar-arrow-color: #f100f1;
  scrollbar-track-color: #fcfcfc;
  scrollbar-darkshadow-color: #93b400;
  scrollbar-base-color: #fcfcfc;
}
#mainxx {
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

.editor {
  width: 1000px;
  height: 800px;
}

.monaco-editor vs {
  text-align: left;
}

.margin-view-overlays {
  width: 40px;
  background-color: rgb(34, 34, 34);
}

.view-lines {
  background-color: rgb(52, 52, 52);
}

.current-line {
  background-color: rgb(114, 114, 114);
}
</style>