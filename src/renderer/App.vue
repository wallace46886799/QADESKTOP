<template>
  <div >
    <div @keyup='submit($event)'>

      <mu-appbar  >
        <mu-flat-button flat class='quantaxislogo'  color='white' style='-webkit-app-region: drag'
          disable slot='left'>QUANTAXIS</mu-flat-button>
        <mu-raised-button slot='right' label='User' ref='button'  @click=toggle(true) />

      </mu-appbar>


      <mu-paper>
        <mu-drawer :open='open' :docked=false @close="toggle()" :right="position === 'right'">
          <mu-appbar  title="QUANTAXIS">

          </mu-appbar>

            <mu-menu >

              <router-link to='/personal/index'>
                <mu-list-item class='menu_list'  title='个人中心'></mu-list-item>
              </router-link>
              <!-- <router-link to='/personal/block'>
                <mu-list-item class='menu_list'  title='版块监控'></mu-list-item>
              </router-link>
 -->
              <router-link to='/personal/backtest'>
                  <mu-list-item title='回测概览'></mu-list-item>
              </router-link>
              <router-link to='/personal/account'>
                <mu-list-item class='menu_list'  title='账户管理'></mu-list-item>
              </router-link>
              <router-link to='/personal/trade'>
                  <mu-list-item title='交易'></mu-list-item>
              </router-link>
              <mu-divider />
              <mu-list-item class='menu_list'  title='Sign out' @click='signout' />


              <mu-list-item  class='menu_list'  @click="open = false" button>
                
              </mu-list-item>
            </mu-menu>
        </mu-drawer>
      </mu-paper>
      <mu-content-block>
        <router-view></router-view>
      </mu-content-block>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'app',
    data () {
      return {
        open: false,
        docked: false,
        item: '登录',

        position: 'left'
      }
    },
    // mounted () {
    //   this.trigger = this.$refs.button.$el
    // },
    methods: {
      submit (e) {
        console.log(e.keyCode)
        if (e.keyCode === 112) {
          this.$router.push('/personal/index')
        }
        if (e.keyCode === 113) {
          this.$router.push('/personal/setting')
        }
      },
      toggle () {
        this.open = !this.open
      },
      handleClose (e) {
        this.open = false
      },
      signout () {
        sessionStorage.clear()
        this.$router.push('/sign')
      }
    },

    watch: {
      topPopup (val) {
        if (val) {
          setTimeout(() => {
            this.item = '注销'
          }, 800)
        }
      }
    }
  }
</script>

<style>
#app {
  font-family: Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

body, html {
 background-color: rgb(52, 52, 52);
}


.mu-flat-button-wrapper {
  font-size: 1.8em;
}
  
.mu-item-content {
  font-size: 18px;
  text-align: middle;
}

.user_pad {
  background-color: #2c3e50;
}
.demo-paper {
  display: inline-block;
  height: 100px;
  width: 100px;
  margin: 20px;
  text-align: center;
}
.mu-circle {
  margin: 0 auto;
}

.mu-menu{
  height: 100%;
  background-color: rgb(52,52,52);
  
}
.mu-appbar{
  background-color: rgb(26,26,26);
  color: white;
}
.mu-raised-button,.mu-menu ,.mu-menu-list,.mu-item, .mu-item-title, menu_list,left{
  background-color: rgb(52,52,52);
  color: white;
}



</style>
