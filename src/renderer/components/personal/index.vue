<template>
  <div >
      <h1>>hi! {{user}}</h1>
      <div class='inside_list'>
        <mu-list  style='float:left'>
          <router-link to='/personal/index/chart'>
              <mu-list-item title='行情中心'></mu-list-item>
          </router-link>
          <router-link to='/personal/backtest'>
              <mu-list-item title='回测概览'></mu-list-item>
          </router-link>
          <router-link to='/personal/account/bind'>
              <mu-list-item title='绑定账户'></mu-list-item>
          </router-link>
          <router-link to='/personal/trade'>
              <mu-list-item title='交易'></mu-list-item>
          </router-link>

        </mu-list>
      </div>
      <div id='views' style='float:left'>
        <router-view>
        </router-view>
      </div>
      
  </div>


</template>

<script>
export default {
  data () {
    return {
      user: sessionStorage.user
    }
  }
}
</script>



<style>
body{
  background-color: rgb(52, 52, 52);
  color: white;
}
.mu-list-item{
  background-color: rgb(52, 52, 52);
  text-align: center;
  align:center;
}
.mu-item{
  background-color: rgb(52, 52, 52);
}
.mu-item-title{
  background-color: rgb(52, 52, 52);
  color: white;
}

.demo-list-wrap {
  width: 1000px;
  height: 1000px;
}

#table_view {
  width: 1000px;
  height: 1000px;
}
#menu_view {
  width: 200px;
  height: 1000px;
}
#list_view {
  width: 150px;
  height: 1000px;
}
#charts_view {
  width: 800px;
  height: 1000px;
}

.inside_list{
   width: 200px;
   height: 1000px;
   float: left;
}

.mu-item-wrapper{
  width:100%;
}
  
#view {
  width: 1800px;
  height: 1000px;
  float: left;
}


#views{
    width:1200px;
    height: 1000px;

    position: relative;
    display: inline-block;
}
.mu-item-content {
    font-size: 18px;
    text-align: middle;
  }
.inside_list{
   width: 150px;
   height: 1000px;
   display: block;
   display: inline-block;
}
</style>