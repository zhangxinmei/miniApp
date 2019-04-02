<template>
  <div class="question">
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
      videoContext: "",
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

  methods: {
    handleRecordStart() {
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
    }
  }
};
</script>

<style scoped>
.question {
  
}
</style>

