<template>
  <div class="question">
    <div class="question-topic"
      v-show="showTopicTitle">
      <div class="topic-title">
        {{currentData.title}}
      </div>
    </div>
    <div v-if="showCount">
      <div class="countdown">
      </div>
      <div class="text">{{count}}</div>
    </div>
    <div v-if="showCamera"
      class="camera">
      <camera flash="on"
        device-position="front"
        style="width:80%; height: 50vh;margin:0 auto;"></camera>
    </div>
    <div v-if="showNextBtn"
      class="next-btn">
      <button class="btn-red"
        type="warn"
        @click="handleNextTopic">下一题</button>
    </div>
    <div class="vedio-countdown"
      v-if="showVedioCountdown">
      <div class="countdown-bg">
        <div class="countdown-text">{{vedioCount}}</div>
      </div>
    </div>
  </div>
</template>

<script>
import countdown from "@/components/countdown";
import { interviewTopics } from "../model/model.js";
import store from "../pages/counter/store.js";
// const recorderManager = wx.getRecorderManager();
const innerAudioContext = wx.createInnerAudioContext();

export default {
  props: ["status"],
  data() {
    return {
      showVedio: true,
      tempFilePath: "",
      videoSrc: "",
      cameraContext: {},
      videoContext: "",
      showCount: false,
      showVedioCountdown: false,
      recorderManager: {},
      showCamera: false,
      animationData: {},
      count: "准备",
      vedioCount: 30,
      currentData: null,
      showTopicTitle: true,
      index: 0,
      topicSet: [],
      showNextBtn: false,
      pageStatus: ""
    };
  },

  components: {
    countdown
  },
  created() {
    this.cameraContext = wx.createCameraContext();
    this.recorderManager = wx.getRecorderManager();
    this.currentData = interviewTopics[this.index];
    setTimeout(() => {
      this.showCount = true;
      this.countDown(4);
    }, 3000);
  },
  // onLoad() {
  //   this.cameraContext = wx.createCameraContext();
  // },

  methods: {
    handleRecordStart() {
      const options = {
        duration: 60000,
        sampleRate: 44100,
        numberOfChannels: 1,
        encodeBitRate: 192000,
        format: "mp3",
        frameSize: 50
      };
      this.recorderManager.start(options);
      this.recorderManager.onStart(() => {
        console.log("recorder start");
      });
      this.recorderManager.onStop(res => {
        console.log("recorder stop", res);
        const { tempFilePath } = res;
        this.tempFilePath = tempFilePath;
        this.topicSet.push({
          type: this.currentData.type,
          path: tempFilePath,
          id: this.currentData.id,
          title: this.currentData.title
        });
        store.commit("collectRecord", this.topicSet);
        this.recurrentCountdown();
        // const TopicSetLastIndex = store.state.interviewTopicSet.length - 1;
        // if (this.status === "show") {
        //   this.index = TopicSetLastIndex;
        //   this.recurrentCountdown();
        // }
        // if (this.status === "hide") {
        //   this.showNextBtn = true;
        // }
      });
      // 错误回调
      // recorderManager.onError((res) => {
      //   console.log(res)
      // })
    },
    // handleRecordStop() {
    //   recorderManager.stop();
    //   recorderManager.onStop(res => {
    //     this.tempFilePath = res.tempFilePath;
    //     console.log("停止录音", res.tempFilePath);
    //   });
    // },
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
          const { tempVideoPath } = res;
          this.topicSet.push({
            type: this.currentData.type,
            path: tempVideoPath,
            id: this.currentData.id,
            title: this.currentData.title
          });
          store.commit("collectRecord", this.topicSet);
          const TopicSetLastIndex = store.state.interviewTopicSet.length - 1;
          // this.showNextBtn = true;
          this.recurrentCountdown();
          // if (this.status === "show") {
          //   this.index = TopicSetLastIndex;
          //   console.log("index", this.index);
          //   this.recurrentCountdown();
          // } else {
          //   this.showNextBtn = true;
          // }
        }
      });
    },
    recurrentCountdown() {
      console.log("interviewTopicSet", store.state.interviewTopicSet);
      if (this.index < interviewTopics.length - 1) {
        this.showTopicTitle = false;
        this.index = this.index + 1;
        const { type } = interviewTopics[this.index];
        if (type === 2) {
          this.showCamera = false;
        }
        setTimeout(() => {
          // this.index = this.index + 1;
          this.currentData = interviewTopics[this.index];
          this.showTopicTitle = true;
          this.vedioCount = this.currentData.type === 1 ? 30 : 60;
          this.count = 3;
          setTimeout(() => {
            this.showCount = true;
            this.countDown(3);
          }, 3000);
        }, 3000);
      } else {
        console.log(222);
        wx.navigateTo({
          url: "/pages/end/main"
        });
      }
    },
    countDown(count) {
      if (count === 0) {
        this.count = "开始";
        setTimeout(() => {
          this.showCount = false;
          this.showVedioCountdown = true;
          this.showCamera = this.currentData.type === 1;
          if (this.currentData.type === 1) {
            this.vedioCoundown(30);
            this.startRecord();
            setTimeout(() => {
              // console.log(555);
              this.stopRecord();
            }, 30000);
          }
          if (this.currentData.type === 2) {
            this.vedioCoundown(61);
            this.handleRecordStart();
          }
        }, 1000);
        return;
      }
      setTimeout(() => {
        count--;
        this.count = count;
        this.countDown(count);
      }, 1000);
    },
    vedioCoundown(count) {
      if (count === 0) {
        setTimeout(() => {
          this.showVedioCountdown = false;
        }, 1000);
        return;
      }
      setTimeout(() => {
        count--;
        this.vedioCount = count;
        this.vedioCoundown(count);
      }, 1000);
    },
    handleNextTopic() {
      this.showNextBtn = false;
      this.recurrentCountdown();
      console.log(222);
    }
  },
  watch: {
    status(val, oldVal) {
      this.pageStatus = val;
      console.log("pageStatus", this.pageStatus);
      console.log(333);
    }
  }
};
</script>

<style scoped>
@import "../styles/vedio.css";
</style>

