<template>
  <div id="title" @keyup="submit($event)">
    <h1 align="center">QUANTAXIS</h1>
    <h2 align="center">Quantitative Financial FrameWork</h2>
  </div>
</template>
<script>
export default {
  name: "home",
  methods: {
    submit(e) {
      console.log(e);
    }
  }
};
</script>
<style>
body,
html {
  background-color: rgb(52, 52, 52);
}

#title {
  position: relative;
  width: 100%;
  height: 50%;
  margin-top: 20%;
  display: inline-block;
}

#title h1 {
  color: rgb(255, 255, 255);
  font-family: Calibri;
  font-size: 60px;
  position: relative;
  font-weight: normal;
}

#title h2 {
  color: rgb(255, 255, 255);
  font-family: Operator Mono Light;
  font-weight: lighter;
  font-size: 15px;
}
</style>
