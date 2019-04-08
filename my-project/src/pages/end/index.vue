<template>
  <div>
    <div class="vdeio">
      <video id="myVideo"
        src="http://wxsnsdy.tc.qq.com/105/20210/snsdyvideodownload?filekey=30280201010421301f0201690402534804102ca905ce620b1241b726bc41dcff44e00204012882540400&bizid=1023&hy=SH&fileparam=302c020101042530230204136ffd93020457e3c4ff02024ef202031e8d7f02030f42400204045a320a0201000400"
        enable-danmu
        autoplay="true"
        controls
        style="width:100%;"></video>
    </div>
    <button class="btn-red"
      type="warn"
      @click="handleEnd">查看面试</button>
  </div>
</template>

<script>
export default {
  data() {
    return {};
  },

  components: {},

  created() {},

  methods: {
    handleEnd() {
      console.log("播放结束");
      // this.showVedio = false;
      // this.showTopic = true;
      wx.navigateTo({
        url: "/pages/counter/main"
      });
    }
  }
};
</script>

<style scoped>
.vdeio {
  margin: 20px 0 0 0;
}
</style>
