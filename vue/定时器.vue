<template>
  <div>null</div>
</template>

<script>
// 开启/清除 定时器
export default {
  data() {
    return {
      timer: null,
    };
  },
  mounted() {
    this.timer = setInterval(() => {
      console.log("1");
    }, 1000);
  },
  beforeDestory() {
    clearInterval(this.timer);
    this.timer = null;
  },
};
</script>

<style>
</style>