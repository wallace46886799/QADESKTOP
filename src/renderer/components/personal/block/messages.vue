<template>
    <div>
        input the code
        <input id='chat'>
        <button @click='sendkey'>send</button>
    </div>
</template>
<script>

const ws = new WebSocket('ws://www.yutiansut.com:8010/command/runbacktest')
const message = []
export default {
  data () {
    return {
      message
    }
  },
  methods: {
    get_message () {
      ws.onmessage = function (event) {
        var div1 = document.createElement('div')
        div1.innerHTML = event.data
        document.body.appendChild(div1)
      }
    },
    sendkey () {
      ws.send(document.getElementById('chat').value)
    },
    stop () {
      ws.send('stop')
    }
  }
}
</script>
