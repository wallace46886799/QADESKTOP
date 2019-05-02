<template>
  <div id="sign">
    <div id="title">
      <h1>#USER</h1>
      <h2>Welcome to QUANTAXIS</h2>
    </div>
    <div id="textbox" @keydown.13="login">
      <mu-text-field label="账户" hintText="请输入用户名" v-model="valuex" labelFloat/>
      <br>
      <mu-text-field label="密码" hintText="请输入密码" type="password" labelFloat/>
      <br>
      <mu-raised-button label="注册" @click="reg()"/>
      <mu-raised-button label="登陆" @click="login()"/>
    </div>
    <mu-popup position="top" :overlay="false" popupClass="demo-popup-top" :open="topPopup">注册成功</mu-popup>
  </div>
</template>
<style lang='css'>
#sign {
  width: 100%;
  height: 100%;
  display: block;
}

#title {
  position: relative;
  width: 40%;
  height: 100%;
  display: inline-block;
  margin: 18% 8% 18% 12%;
  color: white;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  font-size: 1.8em;
}

#textbox {
  position: relative;
  width: 30%;
  height: 100%;
  display: inline-block;
  margin: 20% 0% 20% 0%;
}

.demo-popup-top {
  width: 100%;
  opacity: 0.8;
  height: 48px;
  line-height: 48px;
  display: flex;
  align-items: center;
  justify-content: center;
  max-width: 375px;
}
</style>
<script>
import axios from "axios";
export default {
  data() {
    return {
      value: "1",
      valuex: "admin",
      topPopup: false
    };
  },
  methods: {
    handleChange(value) {
      this.value = value;
    },
    open(position) {
      this[position + "Popup"] = true;
    },
    close(position) {
      this[position + "Popup"] = false;
    },
    reg() {
      var name = document.getElementsByClassName("mu-text-field-input")[0]
        .value;
      var password = document.getElementsByClassName("mu-text-field-input")[1]
        .value;
      let val = "user=" + name + "&password=" + password;
      axios
        .get("http://www.yutiansut.com:8010/user/signup?" + val)
        .then(response => {
          console.log(response.data);
          if (response.data === "SUCCESS") {
            this.open("top");
          } else {
            alert(response.data);
          }
        })
        .catch(function(error) {
          console.log(error);
        });
    },

    login() {
      var name = document.getElementsByClassName("mu-text-field-input")[0]
        .value;
      var password = document.getElementsByClassName("mu-text-field-input")[1]
        .value;
      let val = "user=" + name + "&password=" + password;
      axios
        .get("http://www.yutiansut.com:8010/user/signin?" + val)
        .then(response => {
          console.log(response.data);
          if (response.data === "SUCCESS") {
            sessionStorage.user = name;
            sessionStorage.password = password;
            this.$router.push("/personal/index");
          }
        })
        .catch(function(error) {
          console.log(error);
        });
    },
    logout() {
      sessionStorage.clear();
    }
  },
  watch: {
    topPopup(val) {
      if (val) {
        setTimeout(() => {
          this.topPopup = false;
        }, 2000);
      }
    }
  }
};
</script>
