<template>
  <div>
    <H3>高德地图</H3>
    <div id="container"></div>
  </div>
</template>

<script>
export default {
  mounted() {
    window.onLoad = function() {
      var map = new AMap.Map("container");
    };
    var url =
      "https://webapi.amap.com/maps?v=1.4.15&key=a68bc8339009bf16b324cd00f678bae8&callback=onLoad";
    var jsapi = document.createElement("script");
    jsapi.charset = "utf-8";
    jsapi.src = url;
    document.head.appendChild(jsapi);
  }
};
</script>

<style scoped>
#container {
  margin:0 auto;
  width: 1000px;
  height:500px
}
</style>