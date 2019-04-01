<template>
  <div @click="clickHandle">

    <!-- <div class="userinfo" @click="bindViewTap">
      <img class="userinfo-avatar" v-if="userInfo.avatarUrl" :src="userInfo.avatarUrl" background-size="cover" />
      <img class="userinfo-avatar" src="/static/images/user.png" background-size="cover" />

      <div class="userinfo-nickname">
        <card :text="userInfo.nickName"></card>
      </div>
    </div>

    <div class="usermotto">
      <div class="user-motto">
        <card :text="motto"></card>
      </div>
    </div> -->

    <!-- <form class="form-container">
      <input type="text" class="form-control" :value="motto" placeholder="v-model" />
      <input type="text" class="form-control" v-model="motto" placeholder="v-model" />
      <input type="text" class="form-control" v-model.lazy="motto" placeholder="v-model.lazy" />
    </form>

    <a href="/pages/counter/main" class="counter">去往Vuex示例页面</a> -->
    <!-- <div class="all">
        <div class="left">
        </div>
        <div class="right">
        </div>
    </div> -->
    <video id="myVideo"
      src="http://wxsnsdy.tc.qq.com/105/20210/snsdyvideodownload?filekey=30280201010421301f0201690402534804102ca905ce620b1241b726bc41dcff44e00204012882540400&bizid=1023&hy=SH&fileparam=302c020101042530230204136ffd93020457e3c4ff02024ef202031e8d7f02030f42400204045a320a0201000400"
      enable-danmu
      @ended="handleEnd"
      danmu-btn
      controls></video>
    <input type="button"
      class="form-control"
      value="开始录音"
      @click="handleRecordStart">
    <input type="button"
      class="form-control"
      value="停止录音"
      @click="handleRecordStop">
    <div>
      <camera flash="on"
        device-position="front"
        style="width: 100%; height: 450px;"></camera>
      <input type="button"
        class="form-control"
        value="开始录像"
        @click="startRecord">
      <input type="button"
        class="form-control"
        value="结束录像"
        @click="stopRecord">
      <!-- <view class="btn-area">
        <button type="primary"
          @click="startRecord">开始录像</button>
      </view>
      <view class="btn-area">
        <button type="primary"
          @click="stopRecord">结束录像</button>
      </view> -->
      <video class="video"
        :src="videoSrc"></video>
      <div>{{videoSrc}}</div>
    </div>
  </div>
</template>

<script>
import card from "@/components/card";
const recorderManager = wx.getRecorderManager();
const cameraContext = wx.createCameraContext();
// const innerAudioContext = wx.createInnerAudioContext()

export default {
  data() {
    return {
      motto: "Hello miniprograme",
      tempFilePath: "",
      videoSrc: "",
      userInfo: {
        nickName: "mpvue",
        avatarUrl: "http://mpvue.com/assets/logo.png"
      }
    };
  },

  components: {
    card
  },

  created() {
    this.videoContext = wx.createVideoContext("myVideo");
    console.log("videoContext", this.videoContext);
  },

  methods: {
    bindViewTap() {
      const url = "../logs/main";
      if (mpvuePlatform === "wx") {
        mpvue.switchTab({ url });
      } else {
        mpvue.navigateTo({ url });
      }
    },
    clickHandle(ev) {
      // console.log('clickHandle:', ev)
      // throw {message: 'custom test'}
    },
    handleRecordStart() {
      const recorderManager = wx.getRecorderManager();
      const options = {
        duration: 10000,
        sampleRate: 44100,
        numberOfChannels: 1,
        encodeBitRate: 192000,
        format: "mp3",
        frameSize: 50
      };
      recorderManager.start(options);
      recorderManager.onStart(() => {
        console.log("recorder start");
      });
      recorderManager.onStop(res => {
        console.log("recorder stop", res);
        // const {tempFilePath} = res
      });
      // 错误回调
      // recorderManager.onError((res) => {
      //   console.log(res)
      // })
    },
    handleRecordStop() {
      recorderManager.stop();
      recorderManager.onStop(res => {
        this.tempFilePath = res.tempFilePath;
        console.log("停止录音", res.tempFilePath);
      });
    },
    handleEnd() {
      console.log("播放结束");
    },
    startRecord() {
      cameraContext.startRecord({
        success: () => {
          console.log("startRecord");
        }
      });
    },
    stopRecord() {
      cameraContext.stopRecord({
        success: res => {
          console.log(res);
          this.videoSrc = res.tempVideoPath;
        }
      });
    }
  },

  created() {
    // let app = getApp()
  }
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
