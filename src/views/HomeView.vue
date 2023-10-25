<template>
  <main >
    Accelerometer
    <article class="accelerometer">
      <span style="display: block;">Max x: {{ rawData.x }}   m/s<sup>2</sup></span>
      <span style="display: block;">Max y: {{ rawData.y }}   m/s<sup>2</sup></span>
      <span style="display: block;">Max z: {{ rawData.z }}   m/s<sup>2</sup></span>
      <span style="display: block;">t: {{ rawData.t }}   ms</span>
    </article>
  </main>
</template>

<script setup lang="ts">
import { ref, type Ref, onMounted } from 'vue';

type Item = {
  x: number;
  y: number;
  z: number;
  t: number;
}

const rawData: Ref<Item> = ref({
  x: 0, y: 0, z: 0, t: 0
});

const setHandler = () => {
  window.addEventListener('devicemotion', (event: DeviceMotionEvent) => {
      if (event.acceleration) {
        const { x, y, z } = event.acceleration;
        if (x !== null && y !== null && z !== null) {
          rawData.value.x = rawData.value.x < x ? x : rawData.value.x
          rawData.value.y = rawData.value.y < y ? y : rawData.value.y
          rawData.value.z = rawData.value.z < z ? z : rawData.value.z
        }
        rawData.value.t = event.interval;
      }
    });
}

onMounted(async () => {
  const dme = new DeviceMotionEvent('devicemotion');
  if ('requestPermission' in dme && typeof dme.requestPermission === 'function') {
    dme.requestPermission().then(res => {
      if (res === 'granted') {
        setHandler();
      }
    })
  } else {
    setHandler();
  }
});

</script>
