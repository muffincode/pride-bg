<script setup>
import axios from 'axios'
import { RouterLink, RouterView, createWebHashHistory } from 'vue-router'
import { onMounted, ref } from 'vue';

const searchQuery = ref('lesbian_2019')

const flagsData = ref([])
const currFlagColours = ref([])

const backgroundStyle = ref('gradient')

const cssGradient = ref('')

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
}

function updateBackground() {
  currFlagColours.value = flagsData.value.find(e => e.id == searchQuery.value).colors

  drawInCanvas()

  if (backgroundStyle.value == "gradient") {
    cssGradient.value = generateLinearGradient(currFlagColours.value)
  } else if (backgroundStyle.value == "blobs") {
    cssGradient.value = currFlagColours.value[0].hexCode
    currFlagColours.value.forEach(c => {
      const percentage1 = 25 + Math.floor(Math.random() * 75);
      const percentage2 = 25 + Math.floor(Math.random() * 75);
      const percentage3 = 25 + Math.floor(Math.random() * 75);
      const percentage4 = 25 + Math.floor(Math.random() * 75);
      let percentage11 = 100 - percentage1;
      let percentage21 = 100 - percentage2;
      let percentage31 = 100 - percentage3;
      let percentage41 = 100 - percentage4;
      c.randomRadius = `${percentage1}% ${percentage11}% ${percentage21}% ${percentage2}% / ${percentage3}% ${percentage4}% ${percentage41}% ${percentage31}%`
      c.posLeft = percentage1 + "vh"
      c.posTop = percentage3 + "vh"
    })
  }
}

function rand(min, max) { // min and max included 
  return Math.floor(Math.random() * (max - min + 1) + min)
}

function drawInCanvas() {
  const canvas = document.getElementById('canvas');

  const h = 851
  const w = 393
  const margin = 100
  const nbWaves = currFlagColours.value.length

  if (canvas.getContext) {
    canvas.width = w
    canvas.height = h
    const ctx = canvas.getContext('2d');

    let increment = h / nbWaves
    currFlagColours.value.forEach((c, index) => {
      let pos = increment * (index + 1) + rand(-50, 0)
      ctx.fillStyle = c.hexCode;

      ctx.beginPath();
      ctx.moveTo(0, pos);
      ctx.bezierCurveTo(0 + (w / rand(2, 15)), pos - rand(20, 300), w - (w / rand(2, 15)), pos - rand(20, 300), w, pos)
      ctx.fill();
      ctx.fillRect(0, pos - 1, w, h - (h / pos) + 1);
      if (index === 0) {
        ctx.fillRect(0, 0, w, h);
      }
    })

  }
  /*
  let image = canvas.toDataURL("image/png");
  document.write('<img src="'+image+'"/>');
  */
}

onMounted(() => {
  fetch()

})
</script>

<template>
  <section>

    <canvas id="canvas">

    </canvas>

    <!-- <div class="shapes-container" v-if="backgroundStyle == 'blobs'">
      <div class="shape" v-for="color in currFlagColours" :id="color.id"
        :style="{ background: color.hexCode, borderRadius: color.randomRadius, left: color.posLeft, top: color.posTop }">
      </div>
    </div> -->
    <main>
      <select v-model="searchQuery" id="" @change="updateBackground()">
        <option v-for="flag in flagsData" :value="flag.id">{{ flag.name }} ({{ flag.year }})</option>
      </select>

      <div class="colours">
        <div v-for="c in currFlagColours" :style="{ background: c.hexCode }">{{ c.name }}</div>
      </div>

      <br>

      <button @click="updateBackground()">Generate</button>
    </main>

  </section>

  <RouterView />
</template>

<style scoped lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;700&display=swap');

html,
body,
#app {
  font-family: "Playfair Display", Arial, serif;
  margin: 0;
  padding: 0;
}

* {
  font-family: "Playfair Display";
}

canvas {
  background: grey;
  border: 5px solid white;
  border-radius: 10px;
  box-shadow: 0 0 50px rgba(0, 0, 0, 0.2);
}

section {
  width: 100vw;
  height: 100vh;
  background: rgb(220, 46, 133);
  background: v-bind('cssGradient');
  filter: brightness(0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 2em;

  .shape {
    width: 33vw;
    height: 33vw;
    position: absolute;
  }

  main {
    background: white;
    padding: 1em;
    border-radius: 10px;

    input {
      padding: 1em;
      border: 0;
      opacity: 0.5;
    }

    select {
      padding: 0.5em;
      width: 100%;
      font-weight: bold;
      font-size: 100%;
    }

    div.colours {
      margin-top: 1em;
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 0.3em;
      div {
        padding: 1em;
        text-align: center;
        border-radius: 3px;
        color: #eaeaea;
        box-shadow: 0 0 2px rgba(0,0,0,0.1);
      }
    }
  }
}</style>
