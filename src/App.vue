<script setup>
import axios from 'axios'
import { RouterLink, RouterView, createWebHashHistory } from 'vue-router'
import { onMounted, ref, reactive } from 'vue';

const searchQuery = ref('lesbian_2019')

const flagsData = ref([])
const currFlagColours = ref([])

const backgroundStyle = ref('gradient')

const cssGradient = ref('')
const showAdvanced = ref(true)

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

  // update website background with pride colours
  cssGradient.value = generateLinearGradient(currFlagColours.value)
}

function rand(min, max) { // min and max included 
  return Math.floor(Math.random() * (max - min + 1) + min)
}

const height = ref(851)
const width = ref(393)

let amplitude = reactive({ l: height.value / 10, r: height.value / 10, rand: 0 })
let lift = reactive({ l: 0, r: 0 }) // 0 is default, h/2 is big lift
let flatness = reactive({ l: width.value / 2, r: width.value / 2, rand: 0 }) // 1 is very WoW, w is very flat
let wavesHeight = ref(0) // 0 is equal, -50 is unequal

function switchRes() {
  let t = height.value
  height.value = width.value
  width.value = t
}

function shapes(name) {
  switch (name) {
    case "hills":
        amplitude.l = 0
        amplitude.r = 1
        amplitude.rand = 666
        drawInCanvas()
      break;
  
    default:
      break;
  }
}

function drawInCanvas() {
  const canvas = document.getElementById('canvas');

  amplitude.l = Math.round(amplitude.l)
  amplitude.r = Math.round(amplitude.r)
  amplitude.rand = Math.round(amplitude.rand)

  // if (width.value > window.innerWidth) {
  //   canvas.style.transform = "scale(0.5)"
  // }

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
      let pos = increment * (index) - wavesHeight.value
      ctx.fillStyle = c.hexCode;

      let from = { x: 0, y: pos - lift.l }
      let to = { x: w, y: pos - lift.r }

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
      <button class="button  is-success" @click="() => shapes('hills')">Waves</button>

      <h2 class="title is-5" style="display: flex; align-items: center; gap: 10px;">
        Advanced parameters <button class="button is-small is-light" @click="showAdvanced = !showAdvanced">show</button>
      </h2>
      <section v-if="showAdvanced">

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
              <input @input="drawInCanvas()" type="range" :min="-height / 2" :max="height / 2" v-model="wavesHeight">
              <span class="tag is-info">{{ wavesHeight }}</span>
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


      <button @click="drawInCanvas()">Generate</button>
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
  background: grey;
  border: 5px solid white;
  border-radius: 10px;
  box-shadow: 0 0 50px rgba(0, 0, 0, 0.2);

  max-width: 50vw;
  max-height: 90vh;
}

section {
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
}</style>
