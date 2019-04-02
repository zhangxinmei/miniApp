<template>
  <div>
    <!-- <div class="vdeio"
      v-show="showVedio">
      <video id="myVideo"
        src="http://wxsnsdy.tc.qq.com/105/20210/snsdyvideodownload?filekey=30280201010421301f0201690402534804102ca905ce620b1241b726bc41dcff44e00204012882540400&bizid=1023&hy=SH&fileparam=302c020101042530230204136ffd93020457e3c4ff02024ef202031e8d7f02030f42400204045a320a0201000400"
        enable-danmu
        @ended="handleEnd"
        danmu-btn
        controls></video>
    </div> -->
    <vedioQuestion />
  </div>
</template>

<script>
import card from "@/components/card";
import vedioQuestion from "@/components/vedioQuestion";

export default {
  data() {
    return {
      showVedio: true,
      recorderManager: ""
    };
  },

  components: {
    card,
    vedioQuestion
  },

  created() {
    this.videoContext = wx.createVideoContext("myVideo");
  },

  methods: {}
};
</script>

<style scoped>
.userinfo {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.userinfo-avatar {
  width: 128rpx;
  height: 128rpx;
  margin: 20rpx;
  border-radius: 50%;
}

.userinfo-nickname {
  color: #aaa;
}

.usermotto {
  margin-top: 150px;
}

.form-control {
  display: block;
  padding: 0 12px;
  margin-bottom: 5px;
  border: 1px solid #ccc;
}
.all {
  width: 7.5rem;
  height: 1rem;
  background-color: blue;
}
.all:after {
  display: block;
  content: "";
  clear: both;
}
.left {
  float: left;
  width: 3rem;
  height: 1rem;
  background-color: red;
}

.right {
  float: left;
  width: 4.5rem;
  height: 1rem;
  background-color: green;
}
</style>
