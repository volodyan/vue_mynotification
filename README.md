# vue_notify

## Project setup

```
yarn install
```

### Compiles and hot-reloads for development

```
yarn serve
```

### Compiles and minifies for production

```
yarn build
```

# 自定义 Notification 组件

## 介绍

```
    Element UI Notification通知组件自定义的部分不能满足需要，Element的Notification通知组件
  只能在四个位置出现，分别是top-right/top-left/bottom-right/bottom-left，有些需求并不仅限这四个位置。还有要在该组件内部自定义
  js逻辑代码。所以lement UI Notification通知组件不能满足自定义需求。

```

## 实现需求

### 1.写组件

```
在用element的通知的时候，它是通过在body里面插入元素来实现的，但是我觉得一个个的有点散，所以用一个div来包裹住他们，这样一来还可以不用通过js来计算高度来实现排队，反而变得更加简单。通过timeout来记时，实现停留效果，监听timeFlag的变化来决定是否消失该条通知。注册方法的作用在下面详说。(src/components/MyNoticeComponent/index.js) (src/components/MyNoticeComponent/myNotify.vue)
```

#### myNotify.vue 文件

```
<template>
  <transition name="slide-fade">
    <div class="my-notify" v-if="notifyFlag">
      <div class="ShowContent">
        <div class="ShowContent_left">
          <div class="TimeColockIcon">
            <img src="@/assets/img/timeclock.png" alt="" />
          </div>
          <div class="CustomTime">{{ShowTimeDataObj.ShowTimeData}}</div>
          <div class="CustomTimeProgress">
            <el-progress
              :text-inside="false"
              :show-text="false"
              :stroke-width="8"
              :percentage="ShowTimeDataObj.ShowProgressData"
              color="rgba(255, 69, 58, 1)"
            ></el-progress>
          </div>
        </div>
        <div class="ShowContent_right">
          <div class="Onebox">
            <div class="LeftPart">{{content.WaterworksName}}</div>
            <div class="RightPart">{{content.Time}}</div>
          </div>
          <div class="Twobox">
            <div class="LeftPart">
              <div class="LeftPart_one">{{content.FlowOld}}</div>
              <div class="LeftPart_two">
                <div class="LeftPart_two_top">至</div>
                <div class="LeftPart_two_bottom">→</div>
              </div>
              <div class="LeftPart_three">{{content.FlowNew}}</div>
            </div>
            <div class="RightPart">
              <div class="LeftPart_one">{{content.PressureOld}}</div>
              <div class="LeftPart_two">
                <div class="LeftPart_two_top">至</div>
                <div class="LeftPart_two_bottom">→</div>
              </div>
              <div class="LeftPart_three">{{content.PressureNew}}</div>
            </div>
          </div>
          <div class="Threebox">
            <div class="LeftPart">供水量(万m³/日)</div>
            <div class="RightPart">预测压力满足率(%)</div>
          </div>
        </div>
      </div>
      <div class="notify success"></div>
    </div>
  </transition>
</template>
 <script>
export default {
  name: "myNotify",
  data() {
    return {
      NowtimerSetInterval: null,
      InitCutTime: null,
      TimeStamp: null,
      ShowTimeDataObj: { ShowTimeData: null, ShowProgressData: null },
    };
  },

  methods: {
    //初始化、定时N分钟刷新数据
    NowtimeSetintvalfun() {
      this.InitCutTime = this.content.InitCutTime;
      this.TimeStamp = this.content.TimeStamp;
      this.CutdownTimefun();
      this.NowtimerSetInterval = setInterval(() => {
        this.TimeStamp--;
        // console.log(this.TimeStamp)
        this.CutdownTimefun();
      }, 1000);
      this.$once("hook:beforeDestroy", () => {
        window.clearInterval(this.NowtimerSetInterval);
        this.NowtimerSetInterval = null;
      });
    },

    CutdownTimefun() {
      // FlowNew: "17349"
      // FlowOld: "14015"
      // InitCutTime: 271192
      // PressureNew: "1"
      // PressureOld: "0.4"
      // Time: "2022-01-16 16:41:00"
      // TimeStamp: 271192
      // WaterworksName: "大涌水厂"
      //////倒计时
      let ShowTimeData, ShowProgressData;
      if (this.TimeStamp > 0) {
        let hourTime = parseInt(this.TimeStamp / 3600);
        let h = Number(hourTime) < 10 ? "0" + hourTime : hourTime;
        let minuteTime = parseInt((this.TimeStamp % 3600) / 60);
        let m = Number(minuteTime) < 10 ? "0" + minuteTime : minuteTime;
        let secondTime = parseInt((this.TimeStamp % 3600) % 60);
        let s = Number(secondTime) < 10 ? "0" + secondTime : secondTime;

        // ShowTimeData = `${h}:${m}:${s}`;
        ShowTimeData = `${h}:${m}:${s}`;
        ShowProgressData = parseInt((this.TimeStamp / this.InitCutTime) * 100);
      } else {
        ShowTimeData = "00:00";
        ShowProgressData = 0;
      }

      console.log(
        "ShowTimeData,ShowProgressData",
        ShowTimeData,
        ShowProgressData
      );
      // this.ShowTimeData = ShowTimeData;
      // this.ShowProgressData = ShowProgressData;
      this.$set(this.ShowTimeDataObj, "ShowTimeData", ShowTimeData);
      this.$set(this.ShowTimeDataObj, "ShowProgressData", ShowProgressData);
    },
  },
};
</script>
<style lang="scss" scoped>
.my-notify {
  width: 900px;
  .ShowContent {
    // position: relative;
    // right: 10px;
    animation: show cubic-bezier(0.18, 0.89, 0.32, 1.28) 0.5s;
    @keyframes show {
      0% {
        bottom: -350px;
      }
      100% {
        bottom: 0px;
      }
    }
    display: flex;
    flex-flow: row nowrap;
    height: 196px;
    background-image: url("~@/assets/img/调度列表.png");
    background-size: 100% 100%;
    background-repeat: no-repeat;
    .ShowContent_left {
      display: flex;
      flex-flow: column;
      justify-content: center;
      align-items: center;
      width: 191px;
      height: 196px;
      .TimeColockIcon {
      }
      .CustomTime {
        font-family: "DS-DIGI";
        font-size: 30px;
        font-weight: 400;
        color: #ffffff;
        text-shadow: 0px 3px 7px rgba(0, 0, 0, 0.35);
        margin-bottom: 10px;
      }
      .CustomTimeProgress {
        width: 88px;
        height: 8px;
        background: rgba(0, 0, 0, 0);
        border-radius: 2px;
        margin-bottom: 19px;
        /deep/ .el-progress-bar__outer {
          height: 8px;
          background-color: rgba(28, 3, 2, 0.6);

        }
      }
    }
    .ShowContent_right {
      display: flex;
      flex-flow: column;
      justify-content: center;
      align-items: center;
      width: 602px;
      height: 196px;
      margin-left: 30px;
      .Onebox {
        display: flex;
        flex-flow: row nowrap;
        width: 100%;
        .LeftPart {
          margin-right: 55px;
          font-size: 36px;
          font-family: SourceHanSansSC;
          font-weight: bold;
          color: #ffffff;
          text-shadow: 0px 3px 7px rgba(0, 0, 0, 0.35);
        }
        .RightPart {
          font-size: 30px;
          font-family: Source Han Sans CN;
          font-weight: 400;
          color: #ffffff;
        }
      }
      .Twobox {
        display: flex;
        flex-flow: row nowrap;
        justify-content: flex-start;
        align-items: center;
        margin-top: 10px;
        margin-bottom: 10px;
        width: 100%;
        .LeftPart {
          display: flex;
          flex-flow: row nowrap;
          margin-right: 55px;
          .LeftPart_one {
            font-size: 50px;
            font-family: "DS-DIGI";
            font-weight: normal;
            color: #ffffff;

            text-shadow: 0px 3px 7px rgba(0, 0, 0, 0.35);
          }
          .LeftPart_two {
            margin-left: 36px;
            margin-right: 36px;
            .LeftPart_two_top {
              font-size: 20px;
              font-family: Source Han Sans CN;
              font-weight: 400;
              color: rgba($color: #fff, $alpha: 0.5);
              margin-top: 5px;
            }
            .LeftPart_two_bottom {
              color: #fff;
            }
          }
          .LeftPart_three {
            font-size: 50px;
            font-family: "DS-DIGI";
            font-weight: normal;
            color: #fedf7a;

            text-shadow: 0px 3px 7px rgba(0, 0, 0, 0.35);
          }
        }
        .RightPart {
          display: flex;
          flex-flow: row nowrap;
          .LeftPart_one {
            font-size: 50px;
            font-family: "DS-DIGI";
            font-weight: normal;
            color: #ffffff;

            text-shadow: 0px 3px 7px rgba(0, 0, 0, 0.35);
          }
          .LeftPart_two {
            margin-left: 36px;
            margin-right: 36px;
            .LeftPart_two_top {
              font-size: 20px;
              font-family: Source Han Sans CN;
              font-weight: 400;
              color: rgba($color: #fff, $alpha: 0.5);
              margin-top: 5px;
            }
            .LeftPart_two_bottom {
              color: #fff;
            }
          }
          .LeftPart_three {
            font-size: 50px;
            font-family: "DS-DIGI";
            font-weight: normal;
            color: #fedf7a;

            text-shadow: 0px 3px 7px rgba(0, 0, 0, 0.35);
          }
        }
      }
      .Threebox {
        display: flex;
        flex-flow: row nowrap;
        align-items: center;
        width: 100%;
        .LeftPart {
          margin-right: 55px;
          font-size: 30px;
          font-family: Source Han Sans CN;
          font-weight: 400;
          color: rgba($color: #fff, $alpha: 0.5);
        }
        .RightPart {
          font-size: 30px;
          font-family: Source Han Sans CN;
          font-weight: 400;
          color: rgba($color: #fff, $alpha: 0.5);
        }
      }
    }
  }
}

.slide-fade-leave-active {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter,
.slide-fade-leave-to {
  transform: translatey(30px);
  opacity: 0;
}
</style>








```

#### index.js

```
import vue from 'vue'
import myNotify from './myNotify.vue'

// 创建vue组件实例
const notify = vue.extend(myNotify);

//添加通知节点(用来存放通知的元素)
let notifyWrap = document.createElement('div');
notifyWrap.className = "notify-wrap"
    //notifyWrap.style = "position: fixed; right: 200px; top: 90px; transition-duration: .5s;"
document.body.appendChild(notifyWrap);



let myMsg = {
    /**
     * 通知框
     * @content 提示内容;
     * @type 提示框类型，parameter： success，error，warning
     * @time 显示时长
     */
    notify: ({
        content,
        type,
        time = 1500,
    }) => {
        //创建一个存放通知的div
        const notifyDom = new notify({
            el: document.createElement('div'),
            data() {
                return {
                    notifyFlag: true, // 是否显示
                    time: time, //取消按钮是否显示
                    content: content, // 文本内容
                    type: type, // 类型
                    timer: '',
                    timeFlag: false,

                }
            },
            watch: {
                timeFlag() {
                    if (this.timeFlag) {
                        this.notifyFlag = false;
                        window.clearTimeout(this.timer);
                    }
                }
            },
            created() {
                this.NowtimeSetintvalfun(); ///倒计时
                this.timer = setTimeout(() => {
                    this.timeFlag = true;
                }, this.time);

            },
            beforeDestroy() {
                window.clearTimeout(this.timer);
            }
        })

        //往notifyWrap里面添加通知
        notifyWrap.appendChild(notifyDom.$el);
    }
}

//注册
function register() {
    vue.prototype.$myMsg = myMsg
}

export default {
    myMsg,
    register
}
```
### 2.注册

```
进入main.js添加就可以了
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';


Vue.use(ElementUI);


import MyNoticeComponent from "@/components/MyNoticeComponent/index" ///需要引入的部分

Vue.use(MyNoticeComponent.register) ///需要引入的部分
Vue.config.productionTip = false

new Vue({
    router,
    store,
    render: h => h(App)
}).$mount('#app')

```
### 3.调用

```

  this.$myMsg.notify({
              content: InitContent,
              type: "success",
              time: CutTime,
            });
```

```
<template>
  <div class="hello"></div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  mounted() {
    this.OpenNoticeFun();
  },
  methods: {
    OpenNoticeFun() {
      this.$nextTick(() => {
        let InitContent = {
          WaterworksName: "大涌水厂",
          Time: "2022-01-16 16:41:00",
          TimeStamp: parseInt(new Date().getTime() / 1000) + 24 * 60 * 60 * 1,
          FlowOld: "14015",
          FlowNew: "17349",
          PressureOld: "0.4",
          PressureNew: "1",
        };
        let CutTime =
          Number(InitContent.TimeStamp) - parseInt(new Date().getTime() / 1000);
        InitContent.TimeStamp = CutTime > 0 ? CutTime : 0;
        InitContent.InitCutTime = CutTime;
        if (!!CutTime) {
          this.$myMsg.notify({
            content: InitContent,
            type: "success",
            time: CutTime,
          });
          setInterval(() => {
            this.$myMsg.notify({
              content: InitContent,
              type: "success",
              time: CutTime,
            });
          }, 5000);
        }
      });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.hello {
  display: flex;
  flex-flow: row nowrap;
  justify-content: center;
  width: 100%;
  height: 600px;
  background: transparent;
  .mybox {
    width: 300px;
  }
}
</style>

```
### 4.借鉴
```

  1.https://blog.csdn.net/weixin_39394140/article/details/103768034
  2.https://blog.csdn.net/csu_passer/article/details/86477656
```



