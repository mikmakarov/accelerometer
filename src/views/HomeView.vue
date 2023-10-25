<template>
  <main >
    Accelerometer
    <ul>
      <li v-for="(item, index) in rawData">
        {{ item }}
      </li>
    </ul>
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

const rawData: Ref<Item[]> = ref([]);

onMounted(async () => {
  window.addEventListener('devicemotion', (event: DeviceMotionEvent) => {
    if (event.acceleration) {
      rawData.value.push({
        x: event.acceleration.x,
        y: event.acceleration.y,
        z: event.acceleration.z,
        t: event.interval,
      })
    }
  });
});

</script>
