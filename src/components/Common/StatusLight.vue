<template>
  <div class="led-box">
    <div :class="'led-'+status"></div>
  </div>
</template>

<script>
  export default {
    name: 'StatusLight',
    props: {
      status : {
        type : String,
        default : 'red'
      }
    }
  }
</script>

<style scoped>
  .led-box {
    height: 30px;
    width: 25%;
    float: left;
  }
  .led-red {
    margin: 0 auto;
    width: 18px;
    height: 18px;
    background-color: #F00;
    border-radius: 50%;
    box-shadow: rgba(0, 0, 0, 0.2) 0 -1px 7px 1px, inset #441313 0 -1px 9px, rgba(255, 0, 0, 0.5) 0 2px 12px;
  }

  .led-yellow {
    margin: 0 auto;
    width: 18px;
    height: 18px;
    background-color: #FF0;
    border-radius: 50%;
    box-shadow: rgba(0, 0, 0, 0.2) 0 -1px 7px 1px, inset #808002 0 -1px 9px, #FF0 0 2px 12px;
  }

  .led-green {
    margin: 0 auto;
    width: 18px;
    height: 18px;
    background-color: #ABFF00;
    border-radius: 50%;
    box-shadow: rgba(0, 0, 0, 0.2) 0 -1px 7px 1px, inset #304701 0 -1px 9px, #89FF00 0 2px 12px;
  }

  .led-blue {
    margin: 0 auto;
    width: 24px;
    height: 24px;
    background-color: #24E0FF;
    border-radius: 50%;
    box-shadow: rgba(0, 0, 0, 0.2) 0 -1px 7px 1px, inset #006 0 -1px 9px, #3F8CFF 0 2px 14px;
  }
</style>