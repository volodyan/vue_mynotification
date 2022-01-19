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
