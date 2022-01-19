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