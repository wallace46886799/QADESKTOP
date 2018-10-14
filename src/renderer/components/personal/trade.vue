<template>
    <div>
        <h1>>input</h1>
        <input id='run' type="text">
        <!-- <mu-input id='run'></mu-input> -->
        <button @click='sendkey'>send</button>

        <div>
          <monaco-editor
            class="editor"
            v-model="code"
            theme='vs-dark'
            language="python">

            {{this.code}}
          </monaco-editor>
        </div>
    </div>
</template>
<script>
import MonacoEditor from 'vue-monaco'
// const ws = new WebSocket('ws://localhost:8010/command/runbacktest')
const message = []
export default {
  components: {
    MonacoEditor
  },
  data () {
    return {
      websock: null,
      message,
      code: ''
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
    initWebSocket () {
    // 初始化weosocket
      const wsuri = 'ws://localhost:8010/command/runbacktest'
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
    // get_message () {
    //   ws.onmessage = function (event) {
    //     this.code = this.code + event.data
    //   }
    // },
    sendkey () {
      this.websocketsend(document.getElementById('run').value)
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

.view-lines{
  
  background-color: rgb(52, 52, 52);
}

.current-line{

  background-color: rgb(114, 114, 114);
}
</style>