<template>
    <div>
        <h1>> QUANTAXIS TRADER</h1>
        <div class="left_side">
          <mu-select-field v-model="broker_id" label="选择你的券商" @input='cs'>
            <mu-menu-item v-for="text,index in brokers" :key="index" :value="index" :title="text" />
          </mu-select-field>
          <mu-text-field label="账号(account_cookie)" class="demo-divider-form" :underlineShow="true" labelFloat/>
          <mu-text-field label="交易密码(回测不填)" class="demo-divider-form" :underlineShow="true" labelFloat/>
          <mu-text-field label="通讯密码(回测不填)" class="demo-divider-form" :underlineShow="true" labelFloat/>
        </div>
        <div class = 'right_side'>

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
      brokers: ['海通证券', '模拟交易', '回测', 'ctp'],
      focus_trade_account: '',
      broker_id: 0,
      websock: null,
      message,
      code: '',
      command: '',
      account: null,
      account_cookie: null, // ac
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
    this.websocketclose()
  },
  methods: {
    cs (val) {
      // console.log(val)
      this.broker = this.brokers[this.broker_id]
      console.log(this.broker)
    },
    initWebSocket () {
    // 初始化weosocket
      const wsuri = 'ws://localhost:8010/trade'
      this.websock = new WebSocket(wsuri)
      console.log(this.websock)
      this.websock.onopen = this.websocketonopen
      this.websock.onerror = this.websocketonerror
      this.websock.onmessage = this.websocketonmessage
      this.websock.onclose = this.websocketclose
    },
    websocketonopen () {
      console.log('WebSocket连接成功')
    },
    websocketonerror (e) {
      console.log('ERROR')
    },
    websocketonmessage (e) {
      this.code = this.code + '\n' + e.data
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
    }
    // stop () {
    //   ws.send('stop')
    // }
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


.left_side {
  width: 300px;
}

.view-lines{
  
  background-color: rgb(52, 52, 52);
}

.current-line{

  background-color: rgb(114, 114, 114);
}
</style>