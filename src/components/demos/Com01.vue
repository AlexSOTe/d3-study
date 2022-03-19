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
        value: Math.ceil(Math.random() * 1000),
      });
    }
    const margin = { top: 60, right: 30, bottom: 60, left: 60, };
    const svg = d3
      .select('.Com01')
      .append('svg')
      .attr('id', 'svg')
      .attr('style', 'outline:1px solid red')
      .attr('width', window.innerWidth)
      .attr('height', window.innerHeight);
    const svgW = +svg.attr('width');
    const svgH = + svg.attr('height');
    const gW = svgW - margin.top - margin.right;
    const gH = svgH - margin.left - margin.bottom;

    const mainG = svg.append('g')
      .attr('id', 'mainG')



    const xScale = d3.scaleLinear()
      .domain([0, d3.max(arr, d => d.value)!])
      .range([0, gW]);
    const xAxisG = mainG.append('g')
      .attr('id', 'xAxis')
      .attr('transform', `translate(${margin.left},${svgH - margin.bottom})`);
    const xAxis = d3.axisBottom(xScale);
    xAxisG.append('g').call(xAxis);

    const yScale = d3.scaleBand()
      .domain(arr.map(d => d.name))
      .range([0, gH])
      .padding(.1);
    const yAxisG = mainG.append('g')
      .attr('id', 'yAxis')
      .attr('transform', `translate(${margin.left},${margin.top})`)
    const yAxis = d3.axisLeft(yScale);
    yAxisG.append('g').call(yAxis);

    const barG = mainG.append('g').attr('id', 'barG');
    arr.forEach(d => {
      barG.append('rect')
        .attr('width', xScale(d.value))
        .attr('height', yScale.bandwidth())
        .attr('fill', 'lightgreen')
        .attr('x', margin.left)
        .attr('y', yScale(d.name)! + margin.top)
        .attr('rx', 5)
        .attr('ry', 5)
    });

    d3.selectAll('.tick text').attr('font-size', '1.2em');
    d3.selectAll('#svg').attr('transition', 'all ease 300ms');
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
