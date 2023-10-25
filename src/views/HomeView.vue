<template>
  <main >
    Accelerometer
    <article class="accelerometer">
      <span style="display: block;">Max x: {{ rawData.x }}   m/s<sup>2</sup></span>
      <span style="display: block;">Max y: {{ rawData.y }}   m/s<sup>2</sup></span>
      <span style="display: block;">Max z: {{ rawData.z }}   m/s<sup>2</sup></span>
      <span style="display: block;">t: {{ rawData.t }}   ms</span>
    </article>

    <div>
      <h2>Chart</h2>
      <svg></svg>
    </div>
  </main>
</template>

<script setup lang="ts">
 import * as d3 from 'd3';
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

const accData: Ref<Item[]> = ref([
  {
    x: 0, 
    y: 0, 
    z: 0, 
    t: 0
  },
  {
    x: 1, 
    y: 0, 
    z: 0, 
    t: 1
  },
  {
    x: 2, 
    y: 0, 
    z: 0, 
    t: 2
  },
  {
    x: 3, 
    y: 0, 
    z: 0, 
    t: 3
  },
]);
const iteration = ref(0);
const setHandler = () => {
  window.addEventListener('devicemotion', (event: DeviceMotionEvent) => {
    iteration.value++;
      if (event.acceleration) {
        const { x, y, z } = event.acceleration;
        if (x !== null && y !== null && z !== null) {
          rawData.value.x = rawData.value.x < x ? x : rawData.value.x
          rawData.value.y = rawData.value.y < y ? y : rawData.value.y
          rawData.value.z = rawData.value.z < z ? z : rawData.value.z
        }
        rawData.value.t = event.interval;

        if (accData.value.length > 1000) {
          accData.value.shift()
        }
        if (accData.value.length <= 1000) {
          
          if (x !== null && y !== null && z !== null) {
            accData.value.push({ t: iteration.value, x: x, y: y, z: z});
          }
        }

      }
    });
}

onMounted(async () => {
  const dme = new DeviceMotionEvent('devicemotion');
  if ('requestPermission' in dme && typeof dme.requestPermission === 'function') {
    await dme.requestPermission().then((res: string) => {
      if (res === 'granted') {
        setHandler();
      } else {
        alert('Вонючий айфон')
      }
    })
  } else {
    setHandler();
  }
});

onMounted(() => {
  const width = 800;
  const height = 500;
  const data = accData.value;
  const svg = d3.select("svg").attr("width", width).attr("height", height);
  const g = svg.append("g");

  //3. Creating the Chart Axes
  const x = d3
    .scaleTime()
    .domain([0, 1000])
    .rangeRound([0, width]);

  const y = d3
    .scaleLinear()
    .domain([0, 100])
    .rangeRound([height, 0]);

  //4. Creating a Line
  const line = d3
    .line()
    .x(function (d: any) {
      return x(d.t);
    })
    .y(function (d: any) {
      return y(d.x);
    });

  //5. Appending the Axes to the Chart
  g.append("g")
    .attr("transform", "translate(0," + height + ")")
    .call(d3.axisBottom(x));

  g.append("g")
    .call(d3.axisLeft(y))
    .append("text")
    .attr("fill", "#000")
    .attr("transform", "rotate(-90)")
    .attr("y", 6)
    .attr("dy", "0.71em")

  //6. Appending a path to the Chart
  g.append("path")
    .datum(data)
    .attr("fill", "none")
    .attr("stroke", "steelblue")
    .attr("stroke-width", 1.5)
    .attr("d", line);
});




</script>
