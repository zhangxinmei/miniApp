<template>
  <div class="counter-warp">
    <div>
      <ul>
        <li class="recode-item" v-for="item in interviewTopicSet"
          :key="item.id">
          <div>第{{item.title}}</div>
          <div class="record"
            v-if="item.type===2">
            <button @click="handleRecordPlay(item)"
              class="btn-red"
              type="warn">播放录音</button>
          </div>
          <div class="vedio"
            v-if="item.type===1">
            <video class="video"
              :src="item.path"></video>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
// Use Vuex
import store from "./store";
const innerAudioContext = wx.createInnerAudioContext();

export default {
  data() {
    return {
      lists: [
        {
          type: 1,
          path: "",
          id: 1
        },
        {
          type: 2,
          path: "",
          id: 2
        },
        {
          type: 1,
          path: "",
          id: 3
        }
      ],
      videoSrc: ""
    };
  },
  computed: {
    count() {
      return store.state.count;
    },
    interviewTopicSet() {
      return store.state.interviewTopicSet;
    }
  },
  methods: {
    increment() {
      store.commit("increment");
    },
    decrement() {
      store.commit("decrement");
    },
    handleRecordPlay(item) {
      innerAudioContext.autoplay = true;
      innerAudioContext.src = item.path;
      innerAudioContext.play();
      innerAudioContext.onPlay(() => {
        console.log("开始播放");
      });
      innerAudioContext.onError(res => {
        console.log(res.errMsg);
        console.log(res.errCode);
      });
    }
  }
};
</script>

<style>
.counter-warp {
  padding: 10px;
}
.btn-red {
  margin: 10px;
}
.recode-item {
  margin-bottom: 10px;
}
</style>
