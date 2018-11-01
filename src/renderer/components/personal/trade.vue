<template>
    <div>
        <h1>> QUANTAXIS TRADER</h1>
        <div id="trade_container">
          <div id="left_side">
            <div id='login_form'>
              <!-- 账户登录/操作部分 -->
              <mu-raised-button label="添加账户" @click="openDialog"/>
              <mu-dialog :open="loginDialog" title="添加交易账户" @close="closeDialog">

              <mu-select-field v-model="broker_id" label="选择你的券商" @input='change_broker'>
                <mu-menu-item v-for="text,index in brokers" :key="index" :value="index" :title="text" />
              </mu-select-field>

              <!-- 交易端在每次进入界面/刷新以后都会产生重连操作 -->
              <mu-text-field label="账号(account_cookie)" class="demo-divider-form" :underlineShow="true" labelFloat @input="change_accountcookie"/>
              <mu-text-field label="交易密码(回测不填)" class="demo-divider-form" :underlineShow="true"  type='password' labelFloat  @input="change_password"/>
              <mu-text-field label="通讯密码(回测不填)" class="demo-divider-form" :underlineShow="true" type='password' labelFloat @input="change_tpassword"/>
              <mu-raised-button label="添加账户" class="demo-raised-button" @click="send_login"/>
              <mu-raised-button label="高级配置" class="demo-raised-button" @click="openAdvancedSetting"/>
              </mu-dialog>

              <mu-dialog :open="advanceSetting" title="高级设置" @close="closeAdvancedSetting">
                <mu-switch label="风控" labelLeft v-model="risk_control" class="demo-switch" /><br/>
                <mu-text-field label="账号备注" class="demo-divider-form" :underlineShow="true" labelFloat @input="change_accountnickname"/>
                <mu-text-field label="服务器设置" class="demo-divider-form" :underlineShow="true" labelFloat @input="change_serverip"/>
                <mu-text-field label="刷新频率" class="demo-divider-form" :underlineShow="true" labelFloat @input="change_refreshgap"/>
                
                <mu-raised-button label="关闭" class="demo-raised-button" @click="closeAdvancedSetting"/>
              </mu-dialog>
            </div>
            <div id="log_form">
              <!-- logger -->

            </div>

          </div>
          <div class = 'mid_side'>
            <div id="session_account">
              <mu-list  @change="select_account" >
                 <mu-list-item v-for="text,index in session_list" :key="index" :value="index" :title="text"/>
              </mu-list>
            </div>
          </div>
          <div class = 'right_side'>
            <div id="trade_panel">
              <!-- 交易部分 -->
                <mu-text-field label="股票/期货" class="demo-divider-form" :underlineShow="true" labelFloat @input="change_code"/>
                <mu-text-field label="价格" class="demo-divider-form" :underlineShow="true" labelFloat @input="change_price"/>
                <mu-text-field label="数量" class="demo-divider-form" :underlineShow="true" labelFloat @input="change_amount"/>
                <mu-raised-button label="买入" class="demo-raised-button" @click="buy"/>
                <mu-raised-button label="卖出" class="demo-raised-button" @click="sell"/>
            </div>
            <div id="order_panel">
              <!-- 订单部分 -->
              <div> 当前账户 {{this.account}}</div>
              <div> 可用资金 {{this.available_cash}}</div>
              <div> 当前持仓 {{this.hold}}</div>
            </div>
          </div>

        </div>
    </div>
</template>
<script>
import MonacoEditor from 'vue-monaco'
const message = []
export default {
  components: {
    MonacoEditor
  },
  data () {
    return {
      wsuri: 'ws://localhost:8010/trade',
      loginDialog: false,
      advanceSetting: false,
      brokers: ['海通证券', '同花顺模拟交易', '通达信模拟交易', 'QUANTAXIS回测', 'ctp', 'ctp_mini'],
      brokers_py: ['haitong', 'ths_moni', 'tdx_moni', 'quantaxis_backtest', 'ctp', 'ctp_min'],
      focus_trade_account: '',
      broker_id: 0,
      broker: '海通证券',
      password: null,
      tpassword: null,
      websock: null,
      message,
      code: '',
      command: '',
      session_list: [], // 已经登录的账户列表
      account_setting: {
        account: null,
        password: null,
        tpassword: null,
        server_ip: null,
        refresh_account_gap: null,
        risk_control: null
      },
      account: null,
      available_cash: null,
      hold: null,
      account_nickname: null,
      account_cookie: null, // ac
      server_ip: null, // 服务器ip
      refresh_account_gap: 3, // 3秒一刷新
      risk_control: true, // 是否风控
      order: null, // 订单
      price: null, // 价格
      cur_price: null, // 当前价
      order_amount: null, // 订单数量
      order_towards: null, // 订单方向
      order_status: 100, // 订单状态 100 刚创建
      order_id: null, // 订单id
      trade_id: null, // 交易id
      datetime: null, // 时间
      order_time: null, // 发单时间
      trade_time: null // 成交时间
    }
  },
  created () {
    // 页面刚进入时开启长连接
    this.initWebSocket()
  },
  destroyed () {
    // 页面销毁时关闭长连接
    console.log('QUANTAXIS WEBTRADE: === CLOSE ===')
    this.websocketclose()
  },
  methods: {
    get_available_account () {
      var command = 'query$available_account'
      console.log(command)
      this.websocketsend(command)
    },
    get_marketdata (code) {
      var command = 'query$market_data$' + code
      this.websocketsend(command)
    },
    get_accountinfo () {
      var command = 'query$info$' + this.account
      this.websocketsend(command)
    },
    openDialog () {
      this.loginDialog = true
    },
    closeDialog () {
      this.loginDialog = false
    },
    openAdvancedSetting () {
      this.advanceSetting = true
    },
    closeAdvancedSetting () {
      this.advanceSetting = false
    },
    change_broker (val) {
      this.broker = this.brokers_py[this.broker_id]
      console.log(this.broker)
    },
    change_serverip (val) {
      this.server_ip = val
    },
    change_refreshgap (val) {
      this.refresh_account_gap = val
    },
    change_riskmonitor (val) {
      this.risk_control = !this.risk_control
    },
    change_accountcookie (val) {
      this.account_cookie = val
      console.log(this.account_cookie)
    },
    change_accountnickname (val) {
      this.account_nickname = val
    },
    change_password (val) {
      this.password = val
    },
    change_tpassword (val) {
      this.tpassword = val
    },
    change_code (val) {
      this.code = val
    },
    change_price (val) {
      this.price = val
    },
    change_amount (val) {
      this.amount = val
    },
    buy () {
      if (this.account != null) {
        var command = 'trade$' + this.code + '$' + this.price + '$' + this.amount + '$1' + this.trade_time
        console.log(command)
        this.websocketsend(command)
      } else {
        alert('请先选择账户')
      }
    },
    sell () {
      // code/price/amount/towards/time
      if (this.account != null) {
        var command = 'trade$' + this.code + '$' + this.price + '$' + this.amount + '$-1' + this.trade_time
        console.log(command)
        this.websocketsend(command)
      } else {
        alert('请先选择账户')
      }
    },
    select_account (val) {
      console.log(this.session_list[val])
      this.account = this.session_list[val]
      this.get_accountinfo()
    },
    send_login () {
      console.log([this.account_cookie, this.broker])
      // login$account$broker$password$tpassword$serverip
      this.command = 'login$' + this.account_cookie + '$' + this.broker + '$' + this.password + '$' + this.tpassword + '$' + this.server_ip
      console.log(this.command)
      this.websocketsend(this.command)
      // this.code = this.code + '\n' + '> input: ' + this.command
    },
    initWebSocket () {
    // 初始化weosocket
      this.websock = new WebSocket(this.wsuri)
      console.log(this.websock)
      this.websock.onopen = this.websocketonopen
      this.websock.onerror = this.websocketonerror
      this.websock.onmessage = this.websocketonmessage
      this.websock.onclose = this.websocketclose
    },
    websocketonopen () {
      console.log('QUANTAXIS WEBTRADE: === START ===')
    },
    websocketonerror (e) {
      console.log('ERROR')
    },
    websocketonmessage (e) {
      // 前后端协议交互部分
      var res = JSON.parse(e.data)
      if (res['topic'] === 'login') {
        if (res['status'] === 200) {
          console.log('success login')
          this.get_available_account()
        }
      } else if (res['topic'] === 'query_account') {
        if (res['status'] === 200) {
          this.session_list = res['data']
        }
      } else if (res['topic'] === 'open') {
        this.get_available_account()
      } else if (res['topic'] === 'account_info') {
        this.available_cash = res['data']['cash']
        this.hold = res['data']['hold']
      }
    },
    websocketsend (agentData) {
      this.websock.send(agentData)
    },
    sendkey () {
      console.log(this.command)
      this.websocketsend(this.command)
      this.code = this.code + '\n' + '> input: ' + this.command
    },
    websocketclose () {
      console.log('close')
      this.initWebSocket()
    }
    // stop () {
    //   ws.send('stop')
    // }
  },
  mounted () {
    this.$nextTick(function () {
      // this.get_available_account()
    })
  }
}
</script>
<style>
.editor {
  width: 1000px;
  height: 800px;
}
.mu-text-field{
  width: 200px;
}

.monaco-editor vs {
  text-align: left;
}
.container {
  display: flex;
  flex-wrap: wrap;
}
.margin-view-overlays{
  width: 40px;
  background-color: rgb(34, 34, 34);
}

#trade_container{
  position: relative;
}

#left_side {
  width: 200px;
  height: 100px;
  /* border-left: 1px dashed rgb(34, 34, 34);
  border-right: 1px dashed rgb(34, 34, 34); */
  display: block;
  float: left;
}

.mid-side {
  width: 300px;
  height: 1500px;
  display: block;
  float: left;

}

.view-lines{
  
  background-color: rgb(52, 52, 52);
}

.current-line{

  background-color: rgb(114, 114, 114);
}
.mu-dialog {
  width: 300px;
  height: 500px;
}
</style>