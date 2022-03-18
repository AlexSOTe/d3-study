<template>
  <div class="Com01">
  </div>
</template>
<script setup lang="ts">
  import { ref, reactive, onMounted } from 'vue';
  import { useRoute, useRouter } from 'vue-router';
  const route = useRoute();
  const router = useRouter();
  const query = route.query;
  import * as d3 from 'd3';
  import { text } from 'd3';

  function Draw() {
    const arr = [];
    for (var i = 0; i < 12; i++) {
      arr.push({
        name: `NAME_${i}`,
        value: Math.ceil(Math.random() * 100),
      });
    }
    console.log(arr);
    const svg = d3
      .select('.Com01')
      .append('svg')
      .selectAll('.box')
      .attr('width', '100vw')
      .attr('height', '100vh')
      .enter()
      .data(arr)
      .append('rect')
      .attr('class', 'box')
      .text((d: any) => {
        console.log(d.value);
        return d.value
      })

    //.attr('x1', 50)
    //.attr('y1', 50)
    //.attr('x2', 300)
    //.attr('y2', 200)
    //.attr('stroke', 'red')

  }

  onMounted(() => {
    console.log('onMounted Com01');
    Draw();
  });
</script>
<style scoped lang="scss">
  .Com01 {
  }
</style>
