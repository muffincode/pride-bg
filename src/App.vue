<script setup>
import axios from 'axios'
import { RouterLink, RouterView } from 'vue-router'
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
    })
}

function updateBackground() {
  currFlagColours.value = flagsData.value.find(e => e.id == searchQuery.value).colors

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
      c.posLeft = percentage1+"vh"
      c.posTop = percentage3+"vh"
    })
  }
}

onMounted(() => {
  fetch()
})
</script>

<template>
  <section>
    <div class="shapes-container" v-if="backgroundStyle == 'blobs'">
      <div class="shape" v-for="color in currFlagColours" :id="color.id"
        :style="{ background: color.hexCode, borderRadius: color.randomRadius, left: color.posLeft, top: color.posTop }"></div>
    </div>
    <main>
      <select v-model="searchQuery" id="" @change="updateBackground">
        <option v-for="flag in flagsData" :value="flag.id">{{ flag.name }} ({{ flag.year }})</option>
      </select>

      <ul>
        <li v-for="c in currFlagColours" :style="{ background: c.hexCode }">{{ c.name }}</li>
      </ul>
    </main>

  </section>

  <RouterView />
</template>

<style scoped lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Geologica:wght@100;400;700&display=swap');

html,
body,
#app {
  font-family: Geologica, Arial, serif;
  margin: 0;
  padding: 0;
}

* {
  font-family: Geologica;
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
    }
  }
}</style>
