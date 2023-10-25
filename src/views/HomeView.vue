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
  x: number | null;
  y: number | null;
  z: number | null;
  t: number | null;
}

const rawData: Ref<Item> = ref({
  x: 0, y: 0, z: 0, t: 0
});

onMounted(async () => {
  window.addEventListener('devicemotion', (event: DeviceMotionEvent) => {
    if (event.acceleration) {
      rawData.value.x = event.acceleration.x;
      rawData.value.y = event.acceleration.y;
      rawData.value.z = event.acceleration.z;
      rawData.value.t = event.interval;
    }
  });
});

</script>
