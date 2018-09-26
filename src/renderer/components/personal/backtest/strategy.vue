<template>
  <div style='text-align:left'>

      <div>
      <h2 align='left'>> 策略查看</h2>
      </div>
      
      <div class='container'>

      <router-link v-bind:to="{name:'history',id:this.data0}">
        <mu-raised-button label='成交明细' class='demo-raised-button'/>
      </router-link>
      <router-link v-bind:to="{ name:'assets',id:this.data0}">
          <mu-raised-button label='账户表现' class='demo-raised-button' secondary/>
      </router-link>
      <router-link v-bind:to="{name:'strategy',id:this.data0}">
        <mu-raised-button label='策略查看' class='demo-raised-button' primary />
      </router-link>

      <mu-divider />
    </div>
      <div>
        <h3>{{this.title}}</h3>
        <h4>Version: {{this.version}}    Topic:{{this.topic}}
            Last_modified_time:{{this.last_modified_time}}</h4>
      </div>


  <monaco-editor
    class="editor"
    v-model="code"
    theme='vs-dark'
    language="python">

    {{this.code}}
  </monaco-editor>
  </div>
</template>






<script>
import axios from 'axios'
import MonacoEditor from 'vue-monaco'
export default {
  components: {
    MonacoEditor
  },
  data () {
    return {
      code: 'abc',
      last_modified_time: 'none'
    }
  },
  methods: {
    ready (id) {
      axios.get('http://localhost:8010/strategy/content?account_cookie=' + id)
        .then(response => {
          var res = response.data['result'][0]
          this.title = res['name']
          this.code = res['content']
          this.version = res['version']
          this.last_modified_time = res['last_modify_time']
        })
    }
  },
  mounted () {
    this.$nextTick(function () {
      this.ready(this.$route.params.id)
    })
  },
  watch: {
    topPopup (val) {
      if (val) {
        setTimeout(() => {
          this.topPopup = false
        }, 800)
      }
    }
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
