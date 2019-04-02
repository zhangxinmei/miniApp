<template>
  <div class="question">
    <div v-show="showCount">
      <div class="countdown">
      </div>
      <div class="text"> {{count}}</div>
    </div>
    <div>{{topicTime}}秒</div>
    <input type="button"
      class="form-control"
      value="开始录音"
      @click="handleRecordStart">
    <input type="button"
      class="form-control"
      value="停止录音"
      @click="handleRecordStop">
    <input type="button"
      class="form-control"
      value="播放录音"
      @click="handleRecordPlay">
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
      <video class="video"
        :src="videoSrc"></video>
    </div>
  </div>
</template>

<script>
import card from "@/components/card";
const recorderManager = wx.getRecorderManager();
const innerAudioContext = wx.createInnerAudioContext();

export default {
  data() {
    return {
      motto: "Hello miniprograme",
      showVedio: true,
      tempFilePath: "",
      videoSrc: "",
      cameraContext: "",
      count: 3,
      topicTime:30,
      videoContext: "",
      showCount: true,
      recorderManager: "",
      userInfo: {
        nickName: "mpvue",
        avatarUrl: "http://mpvue.com/assets/logo.png"
      }
    };
  },

  components: {
    card
  },
  onLoad() {
    this.cameraContext = wx.createCameraContext();
  },
  created() {
    this.countDown(3);
  },
  methods: {
    handleRecordStart() {
      const options = {
        duration: 600000,
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
    handleRecordPlay() {
      innerAudioContext.autoplay = true;
      innerAudioContext.src = this.tempFilePath;
      innerAudioContext.onPlay(() => {
        console.log("开始播放");
      });
      innerAudioContext.onError(res => {
        console.log(res.errMsg);
        console.log(res.errCode);
      });
    },
    handleEnd() {
      console.log("播放结束");
      this.showVedio = false;
    },
    startRecord() {
      console.log(this.cameraContext);
      this.cameraContext.startRecord({
        success: () => {
          console.log("startRecord");
        },
        fail: () => {
          console.log("fail");
        }
      });
    },
    stopRecord() {
      this.cameraContext.stopRecord({
        success: res => {
          console.log(res);
          this.videoSrc = res.tempVideoPath;
        }
      });
    },
    countDown(count) {
      if (count === 0) {
        this.count = "开始";
        setTimeout(() => {
          this.showCount = false;
        }, 1000);
        return;
      }
      setTimeout(() => {
        count--;
        this.count = count;
        this.countDown(count);
      }, 1000);
    }
  }
};
</script>

<style scoped>
.question {
}
.countdown {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.3);
  z-index: 1;
}
.text {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
  font-size: 60px;
  color: #fff;
  z-index: 2;
}
</style>

