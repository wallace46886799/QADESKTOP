<template>
  <div>
    <div>
      <h2 align='left'>> 交易历史</h2>
    </div>
    <div class="container">
      <mu-raised-button v-on:click='query()' label="成交明细" class="demo-raised-button" primary />
      <router-link :to="{ name:'assets',params: {id:this.message}}">
        <mu-raised-button label="账户表现" class="demo-raised-button" secondary/>
      </router-link>
      
      <router-link :to="{ name:'strategy',params: {id:this.message}}">
        <mu-raised-button label="策略查看" class="demo-raised-button" />
      </router-link>
      <mu-divider />
    </div>
    <mu-table :height="height" :fixedHeader=true :showCheckbox=false>
      <mu-thead>
        <mu-tr>
          <mu-td>账户: {{data0}}</mu-td>
          <mu-td>组别: {{acc['portfolio_cookie']}}</mu-td>
          <mu-td>运行时间: {{acc['running_time']}}</mu-td>
        </mu-tr>
        <!-- <mu-tr>
          <mu-td>账户: {{data0}}</mu-td>
          <mu-td>组别: {{acc['portfolio_cookie']}}</mu-td>
          <mu-td>运行时间: {{acc['running_time']}}</mu-td>
        </mu-tr> -->
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
        <mu-tbody>
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
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      data0: this.$route.params.id,
      height: '800px',
      multiSelectable: true,
      enableSelectAll: false,
      message: this.$route.params.id,
      items: [['2018-08-01', 'RBL8', '4288', '多开', '100', 'xx', 'xxx', '50'],
        ['2018-08-13', 'RBL8', '4250', '多平', '100', 'xx', 'xxx', '70']],
      acc: {},
      total: 180,
      current: 1,
      showSizeChanger: true
    }
  },

  methods: {
    query () {
      console.log(this.message)
      let val = this.message
      console.log(val)
      axios.get('http://localhost:8010/accounts?account_cookie=' + val)
        .then(response => {
          this.acc = response.data['result'][0]
          this.items = this.acc['history']
        })
        .catch(function (error) {
          console.log(error)
        })
    }
  },
  mounted () {
    this.$nextTick(function () {
      this.query()
    })
  }
}
</script>


<style>
.container{
    width: 900px;
    display: inline-block;

}
</style>
