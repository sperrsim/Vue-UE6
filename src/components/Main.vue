<template>
  <div>
    <qrcode text="123"/>
    <input type="text" v-model="code"/>
    <div :style="{background: color}">
    <ColorPicker
        theme="light"
        :color="color"
        :sucker-hide="false"
        :sucker-canvas="suckerCanvas"
        :sucker-area="suckerArea"
        @changeColor="changeColor"
        @openSucker="openSucker"
      />
  </div>
</template>
<script> 
import {qrcode} from 'qrcode'
import { ColorPicker } from 'vue-color-kit'
import 'vue-color-kit/dist/vue-color-kit.css';

export default {
  components:{
    qrcode,
    ColorPicker,
  },
  
  setup()
  {
    let code = "";
    return {};
  }
};
</script>
