<template>
 <div>
    <h3>The easiest way to create QR Code</h3>
    <h5>Generate a QR code and download it!</h5>
    <div class="urlInput">
      <input type="text" v-model="qr.url_input" size="35" @input="urlChanged" />
      &nbsp;
      <input type="text" readonly v-model="color" size="5" />
      <button
        :style="{ background: color }"
        @click="showcolorpicker = showcolorpicker ? false : true"
        class="fileP"
      >
        &nbsp;
      </button>

      <div v-show="showcolorpicker" style="position: absolute">
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
    </div>

    <!--
https://developer.mozilla.org/en-US/docs/Web/CSS/minmax
https://css-tricks.com/snippets/css/complete-guide-grid/

    
    -->
    <div class="genOut">
      <div v-if="qr.output" class="text-center">
        <div v-html="qr.output"></div>
      </div>
    </div>
    <p>The generated QR links to <a :href="getLink(qr.url)" target="_blank">{{ qr.url }}</a><br/>Please verify it works before printing!</p>
  <button @click="downloadSvg" class="fileSave" >
    <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor"
          stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="fileSa">
      <path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"></path>
      <polyline points="7 10 12 15 17 10"></polyline><line x1="12" y1="15" x2="12" y2="3"></line></svg>
      Download SVG
  </button>
  </div>
</template>

<script> 
import { toString } from 'qrcode';
import { saveAs } from 'file-saver';
import { reactive, ref, watch } from 'vue';
import { ColorPicker } from 'vue-color-kit';
import 'vue-color-kit/dist/vue-color-kit.css';

export default {
  name: 'Colorpicker',
  components: {
    ColorPicker,
  },
  setup() {
    let color = ref('#000000');
    let suckerHide = ref(false);
    let suckerCanvas = null;
    let suckerArea = ref([]);
    let showcolorpicker = ref(false);
    const qr = reactive({
      url: '',
      svg_output: '',
      url_input: '',
      url_input_timeout: null,
      color_timeout: null,
    });
    function changeColor(_color) {
      //const { r, g, b, a } = _color.rgba;
      //color.value = `rgba(${r}, ${g}, ${b}, ${a})`;
      color.value = _color.hex;
      generateQr();
    }
    function openSucker(isOpen) {
      if (isOpen) {
        // ... canvas be created
        // this.suckerCanvas = canvas
        // this.suckerArea = [x1, y1, x2, y2]
      } else {
        // this.suckerCanvas && this.suckerCanvas.remove
      }
    }

    function getLink (url)  {
  return url.includes('http://') || url.includes('https://') ? url : 'https://'+url
}

function downloadSvg ()  {
  const blob = new Blob([qr.output], {type: 'image/svg+xml'});
  saveAs(blob, qr.url + ".svg");
}
    /* *************  */
    const generateQr = () => {
      qr.url = qr.url.trim(); // trim spaces

      if (!qr.url) {
        qr.output = null;
        return;
      }
      toString(
        qr.url,
        { color: { dark: color.value, light: '#0000' } },
        (err, string) => {
          if (err) throw err;
          qr.output = string;
          console.log(string);
        }
      );
    };
    function urlChanged() {
      if (qr.url_input_timeout) {
        clearTimeout(qr.url_input_timeout);
      }
      qr.url_input_timeout = setTimeout(() => {
        qr.url = qr.url_input;
        console.log('urlC');
        generateQr();
      }, 800);
    }

    return {
      qr,
      color,
      suckerHide,
      suckerCanvas,
      suckerArea,
      changeColor,
      showcolorpicker,
      openSucker,
      urlChanged,
      getLink,
      downloadSvg
    };
  },
};
</script>
