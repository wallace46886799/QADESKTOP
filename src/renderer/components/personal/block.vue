<template>
  <div id="block">
    <h1>> block</h1>
    <mu-list class="inside_list">
      <router-link to="/personal/block/settings">
        <mu-list-item title="股票-版块设置"></mu-list-item>
      </router-link>
      <router-link to="/personal/block/messages">
        <mu-list-item title="版块异动"></mu-list-item>
      </router-link>
    </mu-list>

    <div id="views">
      <router-view></router-view>
    </div>
  </div>
</template>
<style lang="css">
#views {
  width: 1400px;
  height: 1000px;
  float: left;
}
.mu-item-content {
  font-size: 18px;
  text-align: middle;
}
.inside_list {
  width: 150px;
  height: 1000px;
  float: left;
}
#view {
  margin-top: 20px;
  width: 1600px;
  float: left;
}
</style>