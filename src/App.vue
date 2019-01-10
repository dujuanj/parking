<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: "App",
  methods: {
    fn() {}
  },
  mounted() {
    var title = document.getElementById("title").innerHTML;
    console.log(title);
    if (title == "") {
      this.$router.push("/");
    }
  },
  watch: {
    $route: {
      handler: function(val, oldVal) {
        console.log(val);
        console.log(oldVal);
        var title = document.getElementById("title").innerHTML;
        console.log(title);
        if (title == "") {
          this.$router.push("/");
        }
      },
      // 深度观察监听s
      deep: true
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 0;
  padding: 0;
}
</style>
