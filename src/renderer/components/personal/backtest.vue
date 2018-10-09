<template>
  <div id='acc'>
      <h1>> BACKTEST</h1>
      <div>
        <mu-raised-button @click="get_strategymember">refresh</mu-raised-button>
      </div>
      
      <!-- <div  class='inside_list'>
        <mu-list>
        <router-link :to="{ name:'history',params: {id:cookie}}">
            <mu-list-item title='历史记录'></mu-list-item>
        </router-link>

        <router-link to='/personal/backtest/strategy'>
            <mu-list-item title='策略查看'></mu-list-item>
        </router-link>
        <router-link to='/personal/backtest/assets'>
            <mu-list-item title='资金曲线'></mu-list-item>
        </router-link>
        
        </mu-list>
        </div> -->
        
        <div class='left_side'>
            <mu-table :height="height"  :enableSelectAll="enableSelectAll" :multiSelectable="multiSelectable"
                      :selectable="selectable" :showCheckbox="showCheckbox"  @rowClick="jump">


            <mu-thead  slot="header">
              <mu-tr>
                <mu-th>组别</mu-th>
                <mu-th>策略</mu-th>
                <mu-th>开始</mu-th>
                <mu-th>结束</mu-th>
                <mu-th>市场</mu-th>
              </mu-tr>
            </mu-thead>
            <template v-for="item in itemDX">
              <mu-tbody>
                  <mu-tr >
                      <mu-td>{{ item['portfolio_cookie']}}</mu-td>
                      <mu-td>{{ item['account_cookie']}}</mu-td>
                      <mu-td>{{ item['start_date']}}</mu-td>
                      <mu-td>{{ item['end_date']}}</mu-td>
                      <mu-td>{{ item['market_type']}}</mu-td>

                  </mu-tr>
              </mu-tbody>
            </template>


        <!-- <mu-tfoot slot="footer">
          <mu-tr>
            <mu-td>ID</mu-td>
            <mu-td>Name</mu-td>
            <mu-td>Status</mu-td>
          </mu-tr>
        </mu-tfoot> -->
      </mu-table>
        </div>

        <div id='viewx'>
          <router-view>
          </router-view>
        </div>

  </div>

  
</template>

<script>
import axios from 'axios'
export default {
  data () {
    return {
      height: '800px',
      selectable: false,
      multiSelectable: false,
      enableSelectAll: false,
      showCheckbox: false,
      user: sessionStorage.user,
      itemDX: [{
        'user_cookie': 'admin',
        'portfolio_cookie': 'macd_test',
        'account_cookie': 'future fast',
        'strategy': 'test_strategy',
        'start_time': '2017-12-01',
        'end_time': '2018-09-15',
        'annualized_returns': '35%'
      }]
    }
  },
  methods: {
    get_strategymember () {
      axios.get('http://localhost:8010/accounts/all')
        .then(response => {
          this.itemDX = response.data['result']
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    jump (index, tr) {
      // console.log(tr)
      var cookie = tr.$children[1].$el.innerText
      console.log(cookie)
      this.$router.replace({name: 'assets', params: {id: cookie}})
    }
  },
  mounted () {
    this.$nextTick(function () {
      this.get_strategymember()
    })
  },
  watch: {
    rowClick (index, tr) {
      console.log(index)
      console.log(tr)
    },
    '$route' (to, from) {
      console.log(to)
      console.log(from)
      this.$router.go(0)
    }
  }
}
</script>




<style lang="css">
#viewx {
  padding-left: 100px;
  width: 1200px;
  height: 1000px;
  display: inline-block;
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

  width:500px;
  height: 1000px;
  display: block;
  float: left;
}
.mu-table, .mu-tr, .mu-td{
    background-color: rgb(52, 52, 52);
    color: white;
}

</style>