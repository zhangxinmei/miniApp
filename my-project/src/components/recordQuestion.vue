<template>
  <div class="question">
    <!-- <countdown /> -->
    <div v-if="showCount">
      <div class="countdown">
      </div>
      <div class="text"> {{count}}</div>
    </div>
    <div>
      <div class="question-topic">
        1.请简单介绍一下自己
      </div>
      <div class="question-tip"
        v-show="showVedioTip">{{vedioTip}}</div>
      <!-- <div v-show="showVedioPlay">
        <button @click="handleRecordPlay"
          class="btn-red"
          type="warn">播放录音</button>
      </div> -->
    </div>
  </div>
</template>

<script>
import countdown from "@/components/countdown";
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
      showCount: false,
      showVedioPlay: false,
      showVedioTip: false,
      recorderManager: "",
      animationData: {},
      count: "面试马上开始",
      vedioTip: "录音中..."
    };
  },

  components: {
    countdown
  },
  created() {
    setTimeout(() => {
      this.showCount = true;
      this.countDown(3);
    }, 3000);
  },
  onLoad() {
    this.cameraContext = wx.createCameraContext();
  },
  onShow() {
    const animation = wx.createAnimation({
      duration: 1000,
      timingFunction: "ease",
      delay: 2000
    });
    this.animation = animation;
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
        this.showVedioTip = true;
        console.log("recorder start");
      });
      recorderManager.onStop(res => {
        this.vedioTip = "时间到，录音结束";
        this.showVedioPlay = true;
        console.log("recorder stop", res);
        const { tempFilePath } = res;
        this.tempFilePath = tempFilePath;
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
      innerAudioContext.play();
      innerAudioContext.onPlay(() => {
        console.log("开始播放");
      });
      innerAudioContext.onError(res => {
        console.log(res.errMsg);
        console.log(res.errCode);
      });
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
          this.handleRecordStart();
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
.question-text {
}
@keyframes fadeOutLeft {
  from {
    opacity: 1;
  }

  to {
    opacity: 0;
    transform: translate3d(-100%, 0, 0);
    -webkit-transform: translate3d(-100%, 0, 0);
  }
}
@-webkit-keyframes fadeOutLeft {
  from {
    opacity: 1;
  }

  to {
    opacity: 0;
    transform: translate3d(-100%, 0, 0);
    -webkit-transform: translate3d(-100%, 0, 0);
  }
}
.fadeOutLeft {
  animation-name: fadeOutLeft;
  -webkit-animation-name: fadeOutLeft;
  animation-delay: 2s;
  -webkit-animation-delay: 2s;
}
.countdown {
  position: fixed;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 1;
}
.text {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate3d(-50%, -50%, 0);
  font-size: 30px;
  color: #fff;
  z-index: 2;
}
.uestion-topic {
  text-align: center;
}
.question-tip {
  text-align: center;
  margin-top: 100px;
}
.btn-red {
  margin-top: 20px;
  border-radius: 30px;
  width: 120px;
  background: #f74c4c;
  font-size: 16px;
}
</style>

