<template>
  <div id='personal'>
    <!-- <div class='welcome'>
      <mu-list>
        <router-link to='/personal/index'>
          <mu-list-item title='个人中心'></mu-list-item>
        </router-link>
        <router-link to='/personal/block'>
          <mu-list-item title='版块监控'></mu-list-item>
        </router-link>


        <router-link to='/personal/account'>
          <mu-list-item title='账户管理'></mu-list-item>
        </router-link>
        <mu-divider/>
        <router-link to='/personal/setting'>
          <mu-list-item title='设置'></mu-list-item>
        </router-link>
      </mu-list>
    </div> -->
    <div id='view'>
      <router-view></router-view>
    </div>
  </div>
</template>
<script>

</script>
<style lang='css'>
  #personal {
    position: relative;
    width: 2800px;
    height: 1000px;
    display: block;
  }

  .welcome {
    margin-top: 20px;
    margin-right: 30px;
    width: 180px;
    height: 1000px;
    float: left;
    display: inline-block;
  }

  .mu-item-content {
    font-size: 18px;
    text-align: middle;
  }

  #view {
    position: relative;
    width: 1800px;
    height: 1000px;
    display: block;
  }

</style>
