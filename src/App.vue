<script setup>
import axios from 'axios'
import { RouterLink, RouterView, createWebHashHistory } from 'vue-router'
import { onMounted, ref, reactive } from 'vue';

const searchQuery = ref('lesbian_2019')

const flagsData = ref([])
const currFlagColours = ref([])

const backgroundStyle = ref('gradient')

const cssGradient = ref('')
const showAdvanced = ref(false)

function generateLinearGradient(colors) {
  if (!Array.isArray(colors) || colors.length === 0) {
    return '';
  }

  const colorStops = colors.map((color, index) => {
    const stopPosition = (index / (colors.length - 1)) * 100;
    return `${color.hexCode} ${stopPosition}%`;
  });

  const deg = 180 + Math.floor(Math.random() * 30)

  const gradient = `linear-gradient(${deg}deg, ${colorStops.join(', ')})`;
  return gradient;
}

async function fetch() {
  axios.get('https://pride.dev/api/flags')
    .then((res) => {
      flagsData.value = res.data
      updateBackground()
      drawInCanvas()
    })
    .catch((err) => {
      flagsData.value = [{ "name": "Agender", "id": "agender_2014", "year": "2014", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 560\"><path d=\"M0 0h800v560H0z\"></path><path fill=\"#b9b9b9\" d=\"M0 80h800v400H0z\"></path><path fill=\"#fff\" d=\"M0 160h800v240H0z\"></path><path fill=\"#b8f483\" d=\"M0 240h800v80H0z\"></path></svg>", "colors": [{ "name": "grey", "hexCode": "#b9b9b9", "r": 185, "g": 185, "b": 185 }, { "name": "white", "hexCode": "#fff", "r": 255, "g": 255, "b": 255 }, { "name": "green", "hexCode": "#b8f483", "r": 184, "g": 244, "b": 131 }] }, { "name": "Aromantic", "id": "aromantic_2014", "year": "2014", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><path d=\"M0 0h800v480H0z\"></path><path fill=\"#a9a9a9\" d=\"M0 0h800v384H0z\"></path><path fill=\"#fff\" d=\"M0 0h800v288H0z\"></path><path fill=\"#a7d379\" d=\"M0 0h800v192H0z\"></path><path fill=\"#3da542\" d=\"M0 0h800v96H0z\"></path></svg>", "colors": [{ "name": "grey", "hexCode": "#a9a9a9", "r": 169, "g": 169, "b": 169 }, { "name": "white", "hexCode": "#ffffff", "r": 255, "g": 255, "b": 255 }, { "name": "light green", "hexCode": "#a7d379", "r": 167, "g": 211, "b": 121 }, { "name": "dark green", "hexCode": "#3da542", "r": 61, "g": 165, "b": 66 }] }, { "name": "Asexual", "id": "asexual_2010", "year": "2010", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><path fill=\"#fff\" d=\"M0 0h800v480H0z\"></path><path d=\"M0 0h800v120H0z\"></path><path fill=\"purple\" d=\"M0 360h800v120H0z\"></path><path fill=\"#a3a3a3\" d=\"M0 120h800v120H0z\"></path></svg>", "colors": [{ "name": "black", "hexCode": "#000000", "r": 0, "g": 0, "b": 0 }, { "name": "grey", "hexCode": "#a3a3a3", "r": 163, "g": 163, "b": 163 }, { "name": "white", "hexCode": "#ffffff", "r": 255, "g": 255, "b": 255 }, { "name": "purple", "hexCode": "#800080", "r": 128, "g": 0, "b": 128 }] }, { "name": "Bisexual", "id": "bisexual_1998", "year": "1998", "emoji": "🏳⚥", "colors": [{ "name": "magenta", "hexCode": "#d6006f", "r": 214, "g": 0, "b": 111 }, { "name": "lavender", "hexCode": "#724e94", "r": 114, "g": 78, "b": 148 }, { "name": "blue", "hexCode": "#0038a7", "r": 0, "g": 56, "b": 167 }], "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><g fill-rule=\"evenodd\"><path fill=\"#d60270\" d=\"M0 0h800v192H0z\"></path><path fill=\"#9b4f96\" d=\"M0 192h800v96H0z\"></path><path fill=\"#0038a8\" d=\"M0 288h800v192H0z\"></path></g></svg>" }, { "name": "Genderfluid", "id": "genderfluid_2012", "year": "2012", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><path fill=\"#333ebd\" d=\"M0 0h800v480H0z\"></path><path d=\"M0 0h800v384H0z\"></path><path fill=\"#be18d6\" d=\"M0 0h800v288H0z\"></path><path fill=\"#fff\" d=\"M0 0h800v192H0z\"></path><path fill=\"#ff75a2\" d=\"M0 0h800v96H0z\"></path></svg>", "colors": [{ "name": "blue", "hexCode": "#333ebd", "r": 51, "g": 62, "b": 189 }, { "name": "white", "hexCode": "#ffffff", "r": 255, "g": 255, "b": 255 }, { "name": "purple", "hexCode": "#be18d6", "r": 190, "g": 24, "b": 214 }, { "name": "pink", "hexCode": "#ff75a2", "r": 255, "g": 117, "b": 162 }] }, { "name": "Genderqueer", "id": "genderqueer_2011", "year": "2011", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><path fill=\"#4a8123\" d=\"M0 0h800v480H0z\"></path><path fill=\"#fff\" d=\"M0 0h800v320H0z\"></path><path fill=\"#b57edc\" d=\"M0 0h800v160H0z\"></path></svg>", "colors": [{ "name": "white", "hexCode": "#fff", "r": 255, "g": 255, "b": 255 }, { "name": "green", "hexCode": "#4a8123", "r": 74, "g": 129, "b": 35 }, { "name": "purple", "hexCode": "#b57edc", "r": 181, "g": 126, "b": 220 }] }, { "name": "Intersex", "id": "intersex_2013", "year": "2013", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 900 600\"><path fill=\"#FFD800\" d=\"M0 0h900v600H0z\"></path><circle cx=\"450\" cy=\"300\" r=\"147\" fill=\"none\" stroke-width=\"50\" stroke=\"#7902aa\"></circle></svg>", "colors": [{ "name": "purple", "hexCode": "#7902aa", "r": 121, "g": 2, "b": 170 }, { "name": "yellow", "hexCode": "#FFD800", "r": 255, "g": 216, "b": 0 }] }, { "name": "Labrys Lesbian", "id": "labrysLesbian_1999", "year": "1999", "svg": "<svg viewBox=\"0 0 1000 600\" xmlns=\"http://www.w3.org/2000/svg\"><path d=\"M0 0h1000v600H0z\" fill=\"#792491\"></path><path d=\"M500 550L211.325 50h577.35z\"></path><path d=\"M479.667 132.374a162.687 162.687 0 01-108.732-62.373 162.687 162.687 0 000 198.075 162.687 162.687 0 01108.732-62.373zm40.672 73.329a162.687 162.687 0 01108.732 62.373 162.687 162.687 0 000-198.075 162.687 162.687 0 01-108.732 62.373zm-4.067-91.571a16.269 8.134 0 00-32.538 0v347.743a16.269 8.134 0 0032.538 0z\" fill=\"#fff\"></path></svg>", "colors": [{ "name": "purple", "hexCode": "#792491", "r": 121, "g": 36, "b": 145 }, { "name": "white", "hexCode": "#fff", "r": 255, "g": 255, "b": 255 }, { "name": "black", "hexCode": "#000", "r": 0, "g": 0, "b": 0 }] }, { "name": "Lesbian", "id": "lesbian_2010", "year": "2010", "svg": "<svg viewBox=\"0 0 35 21\" xmlns=\"http://www.w3.org/2000/svg\"><path fill=\"#8A1E04\" d=\"M0 0h35v21H0z\"></path><path fill=\"#C54E54\" d=\"M0 0h35v18H0z\"></path><path fill=\"#E4ACCF\" d=\"M0 0h35v15H0z\"></path><path fill=\"#EDEDEB\" d=\"M0 0h35v12H0z\"></path><path fill=\"#D063A6\" d=\"M0 0h35v9H0z\"></path><path fill=\"#B75592\" d=\"M0 0h35v6H0z\"></path><path fill=\"#A40061\" d=\"M0 0h35v3H0z\"></path></svg>", "colors": [{ "name": "dark orange", "hexCode": "#8A1E04", "r": 138, "g": 30, "b": 4 }, { "name": "light red", "hexCode": "#c54e54", "r": 197, "g": 78, "b": 84 }, { "name": "light pink", "hexCode": "#E4ACCF", "r": 228, "g": 172, "b": 207 }, { "name": "light grey", "hexCode": "#EDEDEB", "r": 237, "g": 237, "b": 235 }, { "name": "pink", "hexCode": "#D063A6", "r": 208, "g": 99, "b": 166 }, { "name": "dark pink", "hexCode": "#B75592", "r": 183, "g": 85, "b": 146 }, { "name": "purple", "hexCode": "#A40061", "r": 164, "g": 0, "b": 97 }] }, { "name": "Lesbian", "id": "lesbian_2018", "year": "2018", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 344.488 229.658\"><path fill=\"#fff\" d=\"M0 98.425h344.487v32.808H0z\"></path><path fill=\"#ff9a56\" d=\"M0 65.617h344.487v32.808H0z\"></path><path fill=\"#ef7627\" d=\"M0 32.809h344.487v32.808H0z\"></path><path fill=\"#d52d00\" d=\"M0 0h344.487v32.808H0z\"></path><path fill=\"#d162a4\" d=\"M0 131.234h344.488v32.808H0z\"></path><path fill=\"#b55690\" d=\"M0 164.042h344.488v32.808H0z\"></path><path fill=\"#a30262\" d=\"M0 196.85h344.487v32.808H0z\"></path></svg>", "colors": [{ "name": "white", "hexCode": "#fff", "r": 255, "g": 255, "b": 255 }, { "name": "light orange", "hexCode": "#ff9a56", "r": 255, "g": 154, "b": 86 }, { "name": "orange", "hexCode": "#ef7627", "r": 239, "g": 118, "b": 39 }, { "name": "dark orange", "hexCode": "#d52d00", "r": 213, "g": 45, "b": 0 }, { "name": "pink", "hexCode": "#d162a4", "r": 209, "g": 98, "b": 164 }, { "name": "dark pink", "hexCode": "#b55690", "r": 181, "g": 86, "b": 144 }, { "name": "purple", "hexCode": "#a30262", "r": 163, "g": 2, "b": 98 }] }, { "name": "Lesbian", "id": "lesbian_2019", "year": "2019", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 450\"><path fill=\"#A30262\" d=\"M0 0h800v450H0z\"></path><path fill=\"#D362A4\" d=\"M0 0h800v360H0z\"></path><path fill=\"#FFF\" d=\"M0 0h800v270H0z\"></path><path fill=\"#FF9A56\" d=\"M0 0h800v180H0z\"></path><path fill=\"#D52D00\" d=\"M0 0h800v90H0z\"></path></svg>", "colors": [{ "name": "purple", "hexCode": "#A30262", "r": 163, "g": 2, "b": 98 }, { "name": "pink", "hexCode": "#D362A4", "r": 211, "g": 98, "b": 164 }, { "name": "white", "hexCode": "#FFFFFF", "r": 255, "g": 255, "b": 255 }, { "name": "orange", "hexCode": "#FF9A56", "r": 255, "g": 154, "b": 86 }, { "name": "dark orange", "hexCode": "#D52D00", "r": 213, "g": 45, "b": 0 }] }, { "name": "Nonbinary", "id": "nonbinary_2014", "year": "2019", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 400 240\"><g fill-rule=\"evenodd\"><path d=\"M0 150v30h400v-60H0v30\" fill=\"#9c5cd4\"></path><path d=\"M0 90v30h400V60H0v30\" fill=\"#fff\"></path><path d=\"M0 30v30h400V0H0v30\" fill=\"#fcf434\"></path><path d=\"M0 210v30h400v-60H0v30\" fill=\"#2c2c2c\"></path></g></svg>", "colors": [{ "name": "purple", "hexCode": "#9c5cd4", "r": 156, "g": 92, "b": 212 }, { "name": "white", "hexCode": "#fff", "r": 255, "g": 255, "b": 255 }, { "name": "yellow", "hexCode": "#fcf434", "r": 252, "g": 244, "b": 52 }, { "name": "black", "hexCode": "#2c2c2c", "r": 44, "g": 44, "b": 44 }] }, { "name": "Pansexual", "id": "pansexual_2010", "year": "2010", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><path fill=\"#21b1ff\" d=\"M0 0h800v480H0z\"></path><path fill=\"#ffd800\" d=\"M0 0h800v320H0z\"></path><path fill=\"#ff218c\" d=\"M0 0h800v160H0z\"></path></svg>", "colors": [{ "name": "magenta", "hexCode": "#d6006f", "r": 255, "g": 33, "b": 140 }, { "name": "yellow", "hexCode": "#ffd800", "r": 255, "g": 216, "b": 0 }, { "name": "cyan", "hexCode": "#21b1ff", "r": 33, "g": 177, "b": 255 }] }, { "name": "Philadelphia Pride", "id": "philadelphiaPride_2017", "year": "2017", "colors": [{ "name": "black", "hexCode": "#000", "r": 0, "g": 0, "b": 0 }, { "name": "brown", "hexCode": "#784F17", "r": 130, "g": 79, "b": 23 }, { "name": "red", "hexCode": "#E40303", "r": 228, "g": 3, "b": 3 }, { "name": "orange", "hexCode": "#FF8C00", "r": 255, "g": 140, "b": 0 }, { "name": "yellow", "hexCode": "#FFED00", "r": 255, "g": 237, "b": 0 }, { "name": "green", "hexCode": "#008026", "r": 0, "g": 128, "b": 38 }, { "name": "blue", "hexCode": "#004DFF", "r": 0, "g": 77, "b": 255 }, { "name": "violet", "hexCode": "#750787", "r": 117, "g": 7, "b": 135 }], "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 777 480\"><path fill=\"#750787\" d=\"M0 0h777v480H0z\"></path><path fill=\"#004DFF\" d=\"M0 0h777v420H0z\"></path><path fill=\"#008026\" d=\"M0 0h777v360H0z\"></path><path fill=\"#FFED00\" d=\"M0 0h777v300H0z\"></path><path fill=\"#FF8C00\" d=\"M0 0h777v240H0z\"></path><path fill=\"#E40303\" d=\"M0 0h777v180H0z\"></path><path fill=\"#784F17\" d=\"M0 0h777v120H0z\"></path><path d=\"M0 0h777v60H0z\"></path></svg>" }, { "name": "Pink Union Jack", "id": "pinkUnionJack_2009", "year": "2009", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 600 300\"><path fill=\"#fa87d2\" d=\"M0 0h600v300H0z\"></path><path d=\"M0 0l600 300m0-300L0 300\" stroke=\"#fff\" stroke-width=\"60\"></path><clipPath id=\"a\"><path d=\"M300 150h300v150zv150H0zH0V0zV0h300z\"></path></clipPath><path d=\"M0 0l600 300m0-300L0 300\" stroke=\"#e00034\" stroke-width=\"40\" clip-path=\"url(#a)\"></path><path d=\"M300 0v300M0 150h600\" stroke=\"#fff\" stroke-width=\"100\"></path><path d=\"M300 0v300M0 150h600\" stroke=\"#e00034\" stroke-width=\"60\"></path></svg>", "colors": [{ "name": "white", "hexCode": "#fff", "r": 255, "g": 255, "b": 255 }, { "name": "pink", "hexCode": "#fa87d2", "r": 250, "g": 135, "b": 210 }, { "name": "deep pink", "hexCode": "#e00034", "r": 224, "g": 0, "b": 52 }] }, { "name": "Polysexual", "id": "polysexual_2012", "year": "2019", "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><path fill=\"#1c92f6\" d=\"M0 0h800v480H0z\"></path><path fill=\"#07d569\" d=\"M0 0h800v320H0z\"></path><path fill=\"#f61cb9\" d=\"M0 0h800v160H0z\"></path></svg>", "colors": [{ "name": "pink", "hexCode": "#f61cb9", "r": 246, "g": 28, "b": 185 }, { "name": "green", "hexCode": "#07d569", "r": 7, "g": 213, "b": 105 }, { "name": "blue", "hexCode": "#1c92f6", "r": 28, "g": 146, "b": 246 }] }, { "name": "Pride", "id": "pride_1978_eightStripes", "year": "1978", "colors": [{ "name": "hot pink", "hexCode": "#ff69b4", "r": 255, "g": 105, "b": 180 }, { "name": "red", "hexCode": "#e20a17", "r": 255, "g": 0, "b": 0 }, { "name": "orange", "hexCode": "#ff8e00", "r": 255, "g": 142, "b": 0 }, { "name": "yellow", "hexCode": "#ff0", "r": 255, "g": 255, "b": 0 }, { "name": "green", "hexCode": "#008e00", "r": 0, "g": 142, "b": 0 }, { "name": "turquoise", "hexCode": "#00c0c0", "r": 0, "g": 192, "b": 192 }, { "name": "indigo", "hexCode": "#400098", "r": 64, "g": 0, "b": 152 }, { "name": "violet", "hexCode": "#8e008e", "r": 142, "g": 0, "b": 142 }], "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 600 400\"><path fill=\"#8e008e\" d=\"M0 0h600v400H0z\"></path><path fill=\"#400098\" d=\"M0 0h600v350H0z\"></path><path fill=\"#00c0c0\" d=\"M0 0h600v300H0z\"></path><path fill=\"#008e00\" d=\"M0 0h600v250H0z\"></path><path fill=\"#ff0\" d=\"M0 0h600v200H0z\"></path><path fill=\"#ff8e00\" d=\"M0 0h600v150H0z\"></path><path fill=\"red\" d=\"M0 0h600v100H0z\"></path><path fill=\"#ff69b4\" d=\"M0 0h600v50H0z\"></path></svg>" }, { "name": "Pride", "id": "pride_1978_sevenStripes", "year": "1978", "colors": [{ "name": "red", "hexCode": "#e20a17", "r": 255, "g": 0, "b": 0 }, { "name": "orange", "hexCode": "#ff8e00", "r": 255, "g": 142, "b": 0 }, { "name": "yellow", "hexCode": "#ff0", "r": 255, "g": 255, "b": 0 }, { "name": "green", "hexCode": "#008e00", "r": 0, "g": 142, "b": 0 }, { "name": "turquoise", "hexCode": "#00c0c0", "r": 0, "g": 192, "b": 192 }, { "name": "indigo", "hexCode": "#400098", "r": 64, "g": 0, "b": 152 }, { "name": "violet", "hexCode": "#8e008e", "r": 142, "g": 0, "b": 142 }], "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 575 350\"><path fill=\"#8e008e\" d=\"M0 0h600v350H0z\"></path><path fill=\"#400098\" d=\"M0 0h600v300H0z\"></path><path fill=\"#00c0c0\" d=\"M0 0h600v250H0z\"></path><path fill=\"#008e00\" d=\"M0 0h600v200H0z\"></path><path fill=\"#ff0\" d=\"M0 0h600v150H0z\"></path><path fill=\"#ff8e00\" d=\"M0 0h600v100H0z\"></path><path fill=\"red\" d=\"M0 0h600v50H0z\"></path></svg>" }, { "name": "Pride", "id": "pride_1979", "year": "1979", "emoji": "🏳️‍🌈", "colors": [{ "name": "red", "hexCode": "#e20a17", "r": 226, "g": 10, "b": 23 }, { "name": "orange", "hexCode": "#fd8d25", "r": 253, "g": 141, "b": 37 }, { "name": "yellow", "hexCode": "#feec34", "r": 254, "g": 236, "b": 52 }, { "name": "green", "hexCode": "#108028", "r": 16, "g": 128, "b": 40 }, { "name": "blue", "hexCode": "#0f54fb", "r": 15, "g": 84, "b": 251 }, { "name": "violet", "hexCode": "#751086", "r": 117, "g": 16, "b": 134 }], "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 777 480\"><path fill=\"#750787\" d=\"M0 0h777v480H0z\"></path><path fill=\"#004dff\" d=\"M0 0h777v400H0z\"></path><path fill=\"#008026\" d=\"M0 0h777v320H0z\"></path><path fill=\"#ffed00\" d=\"M0 0h777v240H0z\"></path><path fill=\"#ff8c00\" d=\"M0 0h777v160H0z\"></path><path fill=\"#e40303\" d=\"M0 0h777v80H0z\"></path></svg>" }, { "name": "Progress Pride", "id": "progressPride_2018", "year": "2017", "colors": [{ "name": "white", "hexCode": "#fff", "r": 255, "g": 255, "b": 255 }, { "name": "pink", "hexCode": "#F5A9B8", "r": 245, "g": 169, "b": 184 }, { "name": "light blue", "hexCode": "#5BCEFA", "r": 91, "g": 206, "b": 250 }, { "name": "black", "hexCode": "#000", "r": 0, "g": 0, "b": 0 }, { "name": "brown", "hexCode": "#784F17", "r": 130, "g": 79, "b": 23 }, { "name": "red", "hexCode": "#E20A17", "r": 226, "g": 10, "b": 23 }, { "name": "orange", "hexCode": "#FD8D25", "r": 253, "g": 141, "b": 37 }, { "name": "yellow", "hexCode": "#FEEC34", "r": 254, "g": 236, "b": 52 }, { "name": "green", "hexCode": "#108028", "r": 16, "g": 128, "b": 40 }, { "name": "blue", "hexCode": "#0F54FB", "r": 15, "g": 84, "b": 251 }, { "name": "violet", "hexCode": "#751086", "r": 117, "g": 16, "b": 134 }], "svg": "<svg viewBox=\"0 0 500 300\" xmlns=\"http://www.w3.org/2000/svg\"><g fill=\"none\" fill-rule=\"evenodd\"><path fill=\"#751086\" d=\"M0 250h500v50H0z\"></path><path fill=\"#0F54FB\" d=\"M0 200h500v50H0z\"></path><path fill=\"#108028\" d=\"M0 150h500v50H0z\"></path><path fill=\"#FEEC34\" d=\"M0 100h500v50H0z\"></path><path fill=\"#FD8D25\" d=\"M0 50h500v50H0z\"></path><path fill=\"#E20A17\" d=\"M0 0h500v50H0z\"></path><path fill=\"#000\" d=\"M79.907 0l149.906 149.907L79.907 299.813 5 300V0z\"></path><path fill=\"#784F17\" d=\"M39.907 0l149.906 149.907L39.907 299.813l-40 .187V0z\"></path><path fill=\"#5BCEFA\" d=\"M-.047 0l149.814 149.907L-.14 299.813z\"></path><path fill=\"#F5A9B8\" d=\"M-40.093 0l149.906 149.907-149.906 149.906z\"></path><path fill=\"#FFF\" d=\"M-80.093 0L69.813 149.907-80.093 299.813z\"></path></g></svg>" }, { "name": "South African Pride", "id": "southAfricaPride_2010", "year": "2010", "colors": [{ "name": "black", "hexCode": "#000", "r": 0, "g": 0, "b": 0 }, { "name": "white", "hexCode": "#784F17", "r": 130, "g": 79, "b": 23 }, { "name": "red", "hexCode": "#E40303", "r": 228, "g": 3, "b": 3 }, { "name": "orange", "hexCode": "#FF8C00", "r": 255, "g": 140, "b": 0 }, { "name": "yellow", "hexCode": "#FFED00", "r": 255, "g": 237, "b": 0 }, { "name": "green", "hexCode": "#008026", "r": 0, "g": 128, "b": 38 }, { "name": "blue", "hexCode": "#004DFF", "r": 0, "g": 77, "b": 255 }, { "name": "violet", "hexCode": "#750787", "r": 117, "g": 7, "b": 135 }], "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 720 480\"><path fill=\"#750787\" d=\"M0 0h720v480H0z\"></path><path fill=\"#004DFF\" d=\"M0 0h720v400H0z\"></path><path fill=\"#008026\" d=\"M0 0h720v320H0z\"></path><path fill=\"#FFED00\" d=\"M0 0h720v240H0z\"></path><path fill=\"#FF8C00\" d=\"M0 0h720v160H0z\"></path><path fill=\"#E40303\" d=\"M0 0h720v80H0z\"></path><path fill=\"#FFF\" d=\"M144.222 0l240 160H720v32H374.533l-288-192zm0 480l240-160H720v-32H374.533l-288 192z\"></path><path d=\"M0 57.689L273.467 240 0 422.311v-38.459L215.778 240 0 96.148z\"></path></svg>" }, { "name": "Transgender", "id": "transgender_1999", "year": "1999", "emoji": "🏳️‍⚧️", "colors": [{ "name": "light blue", "hexCode": "#5BCEFA", "r": 91, "g": 206, "b": 250 }, { "name": "pink", "hexCode": "#F5A9B8", "r": 245, "g": 169, "b": 184 }, { "name": "white", "hexCode": "#FFF", "r": 255, "g": 255, "b": 255 }], "svg": "<svg xmlns=\"http://www.w3.org/2000/svg\" viewBox=\"0 0 800 480\"><path fill=\"#5BCEFA\" d=\"M0 0h800v480H0z\"></path><path fill=\"#F5A9B8\" d=\"M0 96h800v288H0z\"></path><path fill=\"#FFF\" d=\"M0 192h800v96H0z\"></path></svg>" }]
      updateBackground()
      drawInCanvas()
    })
}

function updateBackground() {
  currFlagColours.value = flagsData.value.find(e => e.id == searchQuery.value).colors

  drawInCanvas()

  // update website background with pride colours
  cssGradient.value = generateLinearGradient(currFlagColours.value)
}

function downloadImage() {
  const canvas = document.getElementById('canvas');
  let image = canvas.toDataURL("image/png");
  document.write('<img src="' + image + '"/>');
}

function rand(min, max) { // min and max included 
  return Math.floor(Math.random() * (max - min + 1) + min)
}

const height = ref(window.screen.height)
const width = ref(window.screen.width)

let amplitude = reactive({ l: height.value / 10, r: height.value / 10, rand: 0 })
let lift = reactive({ l: 0, r: 0 }) // 0 is default, h/2 is big lift
let flatness = reactive({ l: width.value / 2, r: width.value / 2, rand: 0 }) // 1 is very WoW, w is very flat
let wavesHeight = reactive({ h: 0, rand: 0 }) // 0 is equal, -50 is unequal

function switchRes() {
  let t = height.value
  height.value = width.value
  width.value = t
}

function shapes(basis, name) {
  lift.l = 0
  lift.r = 0
  if (basis == "stripes") {
    switch (name) {
      case "hills":
        amplitude.l = height.value / 10
        amplitude.r = height.value / 10
        amplitude.rand = height.value / 20
        flatness.l = width.value / 5
        flatness.r = width.value / 5
        flatness.rand = width.value / 20
        wavesHeight.rand = -50
        break;
      case "flames":
        amplitude.l = height.value / 4
        amplitude.r = height.value / 4
        amplitude.rand = height.value / 4
        flatness.l = width.value - width.value * 0.1
        flatness.r = width.value - width.value * 0.1
        flatness.rand = width.value / 2
        wavesHeight.h = -150
        break;
      case "stripes":
        amplitude.l = 0
        amplitude.r = 0
        amplitude.rand = 0
        wavesHeight.h = 0
        wavesHeight.rand = 0
        break;
      default:
        break;
    }
    drawInCanvas()
  } else if (basis == "blobs") {
    switch (name) {
      case "big":
        drawBigBlobs()
        break;

      default:
        drawBlobs()
        break;
    }

  }
}

function drawBlob(on, to, from, colour) {
  on.fillStyle = colour;
  let inflPoint1 = { x: from.x, y: from.y - 100 }
  let inflPoint2 = { x: to.x, y: to.y - 100 }

  on.beginPath();
  on.lineWidth = 3;
  on.strokeStyle = colour
  on.moveTo(from.x, from.y);
  on.lineTo(to.x, to.y)
  on.stroke()

  on.moveTo(from.x, from.y);
  on.bezierCurveTo(inflPoint1.x, inflPoint1.y, inflPoint2.x, inflPoint2.y, to.x, to.y)
  on.fill();
  // -----------------------------------------
  on.fillStyle = colour
  inflPoint1 = { x: from.x, y: from.y + 100 }
  inflPoint2 = { x: to.x, y: to.y + 100 }
  on.beginPath();
  on.moveTo(from.x, from.y);
  on.bezierCurveTo(inflPoint1.x, inflPoint1.y, inflPoint2.x, inflPoint2.y, to.x, to.y)
  on.fill();
}

function drawBigBlobs() {
  const canvas = document.getElementById('canvas');

  if (canvas.getContext) {
    canvas.width = width.value
    canvas.height = height.value
    const ctx = canvas.getContext('2d');

    const h = height.value
    const w = width.value

    currFlagColours.value.forEach((c, index) => {
      if (index === 0) {
        ctx.fillRect(0, 0, w, h);
      }

      let from = { x: rand(0, w), y: rand(0, h) }
      let to = { x: rand(0, w), y: rand(0, h) }

      drawBlob(ctx, to, from, c.hexCode)
    })
  }
}

function drawBlobs() {
  const canvas = document.getElementById('canvas');

  if (canvas.getContext) {
    canvas.width = width.value
    canvas.height = height.value
    const ctx = canvas.getContext('2d');

    const h = height.value
    const w = width.value

    ctx.fillStyle = currFlagColours.value[0].hexCode;
    ctx.fillRect(0, 0, w, h);

    for (let i = 0; i < 60; i++) {
      let index = rand(1, currFlagColours.value.length - 1)

      let from = { x: rand(0, w), y: rand(0, h) }
      let to = { x: from.x + rand(w / 20, w / 10), y: from.y + rand(-h / 10, h / 10) }

      drawBlob(ctx, to, from, currFlagColours.value[index].hexCode)
    }
  }
}

function drawInCanvas() {
  const canvas = document.getElementById('canvas');

  amplitude.l = Math.round(amplitude.l)
  amplitude.r = Math.round(amplitude.r)
  amplitude.rand = Math.round(amplitude.rand)

  const margin = 100
  const nbWaves = currFlagColours.value.length

  if (canvas.getContext) {
    canvas.width = width.value
    canvas.height = height.value
    const ctx = canvas.getContext('2d');

    const h = height.value
    const w = width.value

    let increment = h / nbWaves

    currFlagColours.value.forEach((c, index) => {
      let pos = increment * (index) - wavesHeight.h - rand(0, wavesHeight.rand)

      let from = { x: 0, y: pos - lift.l }
      let to = { x: w, y: pos - lift.r }

      let grd = ctx.createLinearGradient(from.x + w / 2, from.y - pos, to.x - w / 2, to.y + pos + h / 2);

      grd.addColorStop(0, c.hexCode);
      grd.addColorStop(1, `rgb(${c.r - 30},${c.g - 30},${c.b - 30})`);

      ctx.fillStyle = grd;
      //ctx.fillStyle = c.hexCode;

      let inflPoint1 = { x: flatness.l, y: pos - amplitude.l - lift.l - rand(0, amplitude.rand) }
      let inflPoint2 = { x: w - flatness.r - rand(-flatness.rand, flatness.rand) / 2, y: pos - amplitude.r - lift.r - rand(0, amplitude.rand) }

      ctx.beginPath();
      ctx.moveTo(from.x, from.y);
      ctx.bezierCurveTo(inflPoint1.x, inflPoint1.y, inflPoint2.x, inflPoint2.y, to.x, to.y)
      ctx.fill();
      /* fillPoly */
      ctx.beginPath();
      ctx.moveTo(from.x, from.y - 1);
      ctx.lineTo(0 + w, to.y - 1);
      ctx.lineTo(w, h - (h / pos) + 1);
      ctx.lineTo(0, h - (h / pos) + 1);
      ctx.closePath();
      ctx.fill();
      //ctx.fillRect(0, pos - 1, w, h - (h / pos) + 1);
      if (index === 0) {
        ctx.fillRect(0, 0, w, h);
      }

      /* DEBUG : see infl points
      ctx.fillStyle = "#FF0000";
      ctx.fillRect(inflPoint1.x,inflPoint1.y,10,10); // fill in the pixel at (10,10)
      ctx.fillStyle = "#0000FF";

      ctx.fillRect(inflPoint2.x,inflPoint2.y,10,10); // fill in the pixel at (10,10)
      ctx.fillStyle = c.hexCode;
      */
    })
  }
}

onMounted(() => {
  fetch()
})
</script>

<template>
  <section id="settings">

    <canvas id="canvas">
    </canvas>

    <main>
      <select v-model="searchQuery" id="" @change="updateBackground()">
        <option v-for="flag in flagsData" :value="flag.id">{{ flag.name }} ({{ flag.year }})</option>
      </select>

      <div class="colours">
        <div v-for="c in currFlagColours" :style="{ background: c.hexCode }">{{ c.name }}</div>
      </div>

      <h2 class="title is-5">Parameters</h2>
      <h2 class="title is-6">Size</h2>

      <div class="size">
        <input type="text" class="input" v-model="width">
        <button class="button is-light" @click="switchRes">↔️</button>
        <input type="text" class="input" v-model="height">
        <button class="button is-light is-info" @click="width = 1080; height = 1920">Mobile</button>
        <button class="button is-light is-info" @click=" width = 1920; height = 1080">Desktop</button>
      </div>

      <h2 class="title is-6">Shapes</h2>
      <div id="shapes">
        <button class="button is-info" @click="() => shapes('stripes', 'hills')">Hills ⛰️</button>
        <button class="button is-info" @click="() => shapes('stripes', 'flames')">Flames 🔥</button>
        <button class="button is-info" @click="() => shapes('stripes', 'stripes')">Stripes 🏳️‍🌈</button>
        <button class="button is-info" @click="() => shapes('blobs', 'big')">Big blobs ☁️</button>
        <button class="button is-info" @click="() => shapes('blobs')">Small blobs ☁️</button>
      </div>

      <h2 class="title is-5" style="display: flex; align-items: center; gap: 10px;">
        Advanced parameters <button class="button is-small is-light" @click="showAdvanced = !showAdvanced">show</button>
      </h2>
      <section id="advanced" v-if="showAdvanced">

        <div class="controls">
          <p>Vertical shift</p>
          <div class="control">
            <label>Left</label>
            <div>
              <input @input="drawInCanvas()" type="range" :step="height / 20" :min="-height" :max="height"
                v-model="lift.l">
              <span class="tag is-info">{{ lift.l }}</span>
            </div>
          </div>
          <div class="control">
            <label>Right</label>
            <div>
              <input @input="drawInCanvas()" type="range" :step="height / 20" :min="-height" :max="height"
                v-model="lift.r">
              <span class="tag is-info">{{ lift.r }}</span>
            </div>
          </div>
          <div class="control">
            <label>Overall shift</label>
            <div>
              <input @input="drawInCanvas()" type="range" :min="-height / 2" :max="height / 2" v-model="wavesHeight.h">
              <span class="tag is-info">{{ wavesHeight.h }}</span>
            </div>
          </div>
          <div class="control">
            <label>Randomness</label>
            <div>
              <input @input="drawInCanvas()" type="range" :min="-height / 2" :max="height / 2" v-model="wavesHeight.rand">
              <span class="tag is-info">{{ wavesHeight.rand }}</span>
            </div>
          </div>
        </div>

        <div class="controls">
          <p>Vertical variation</p>
          <div class="control">
            <label>Left</label>
            <div>
              <input @input="drawInCanvas()" type="range" :step="height / 30" :max="height" v-model="amplitude.l">
              <span class="tag is-info">{{ amplitude.l }}</span>
            </div>
          </div>
          <div class="control">
            <label>Right</label>
            <div>
              <input @input="drawInCanvas()" type="range" :step="height / 30" :max="height" v-model="amplitude.r">
              <span class="tag is-info">{{ amplitude.r }}</span>
            </div>
          </div>
          <div class="control">
            <label>Randomness</label>
            <div>
              <input @input="drawInCanvas()" type="range" :step="height / 30" :max="height" v-model="amplitude.rand">
              <span class="tag is-primary">{{ amplitude.rand }}</span>
            </div>
          </div>
        </div>

        <div class="controls">
          <p>Horizontal variation</p>
          <div class="control">
            <label>Left</label>
            <div>
              <input @input="drawInCanvas()" type="range" :max="width" v-model="flatness.l">
              <span class="tag is-info">{{ flatness.l }}</span>
            </div>
          </div>
          <div class="control">
            <label>Right</label>
            <div>
              <input @input="drawInCanvas()" type="range" :max="width" v-model="flatness.r">
              <span class="tag is-info">{{ flatness.r }}</span>
            </div>
          </div>
          <div class="control">
            <label>Randomness</label>
            <div>
              <input @input="drawInCanvas()" type="range" :max="width" v-model="flatness.rand">
              <span class="tag is-primary">{{ flatness.rand }}</span>
            </div>
          </div>
        </div>

      </section>


      <button class="button is-large is-success" @click="drawInCanvas()">Generate</button>
      <button class="button is-large is-info" @click="downloadImage()">Download</button>

    </main>

  </section>

  <RouterView />
</template>

<style scoped lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700&family=Poppins:wght@300;400;700&display=swap');

html,
body,
#app {
  font-family: "Poppins", Arial, serif;
  margin: 0;
  padding: 0;
}

* {
  font-family: "Poppins";
}

main::-webkit-scrollbar {
  display: none;
}

.title {
  margin-bottom: 0;
}

canvas {
  background: white;
  border: 5px solid white;
  border-radius: 10px;
  box-shadow: 0 0 50px rgba(0, 0, 0, 0.2);

  max-width: 50vw;
  max-height: 90vh;
}

section#advanced {
  background: white;
  padding: 2em 1em;
  border-radius: 20px;
  transition: background 0.5s ease;

  div.controls {
    width: 80%;
    margin: auto;

    p {
      text-align: center;
      display: flex;
      align-items: center;
      justify-content: space-around;

      &:before {
        content: "";
        display: block;
        width: 20%;
        border-bottom: 1px solid #ccc;
      }

      &:after {
        content: "";
        display: block;
        width: 20%;
        border-bottom: 1px solid #ccc;
      }
    }

    div.control {
      display: flex;
      align-items: center;
      justify-content: space-between;

      div {
        display: flex;
        align-items: center;
        gap: 10px;

        span {
          width: 50px;
        }
      }
    }
  }

}

section#settings {
  width: 100vw;
  height: 100vh;
  background: rgb(220, 46, 133);
  background: v-bind('cssGradient');

  filter: brightness(0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 2em;

  main {
    background: white;
    padding: 2em;
    box-sizing: border-box;
    border-radius: 10px;
    max-height: 80vh;
    overflow: scroll;

    opacity: 0.5;
    transition: opacity 0.6s ease;

    display: flex;
    flex-direction: column;
    gap: 1em;

    &:hover {
      opacity: 1;

      section {
        background: #222021;
        color: white;

      }
    }

    div#shapes {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px
    }

    .size {
      display: flex;
      gap: 10px;
    }

    input {
      padding: 1em;
    }

    select {
      padding: 0.5em;
      width: 100%;
      font-weight: bold;
      font-size: 100%;
    }

    div.colours {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 0.3em;

      div {
        padding: 1em;
        text-align: center;
        border-radius: 3px;
        color: #eaeaea;
        box-shadow: 0 0 2px rgba(0, 0, 0, 0.1);
      }
    }
  }
}


@media screen and (max-width: 520px) {
  canvas {
    max-width: 80vh;
    margin-top: 1em;
  }

  #app>section {
    flex-direction: column;
    width: auto;
    height: auto;

    main {
      overflow: visible;
      opacity: 1;
      width: 96vw;
      max-height: fit-content;
      border-radius: 10px 10px 0 0;

      div.size {
        flex-direction: column;
      }

      div#shapes {
        display: grid;
        grid-template-columns: 1fr;
        gap: 10px
      }
    }
  }
}</style>
