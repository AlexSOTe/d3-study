<template>
  <div class="TreeView"
       ref="TreeView">
  </div>
</template>
<script setup lang="ts">
  import { ref, reactive, onMounted } from 'vue';
  import { useRoute, useRouter } from 'vue-router';
  import * as d3 from 'd3';
  import { treeData } from './treeData';
  import { D3ZoomEvent } from 'd3';

  const route = useRoute();
  const router = useRouter();
  const query = route.query;

  const TreeView = ref<HTMLDivElement | null>(null);


  async function Draw() {
    //节点的样式
    const nodeStyle = {
      width: 100,
      height: 30,
      background: 'green',
      r: 8,
      color: 'white'
    };
    const rootNodeBgColor = 'orange';
    const subNodeBgColor = 'blue';
    const animationDuration = 100;

    const svg = d3.select('.TreeView')
      .append('svg')
      .attr('width', '100vw')
      .attr('height', '100vh');

    const content = svg.append('g').attr('class', 'content');

    const zoom = d3.zoom().on('zoom', (ev) => {
      content.attr('transform', ev.transform);
    });
    // @ts-ignore
    svg.call(zoom);


    const linkG = content.append('g').attr('class', 'linkG');
    const nodeG = content.append('g').attr('class', 'nodeG');

    const tree = d3.tree().nodeSize([nodeStyle.width, nodeStyle.height]);
    const data = d3.hierarchy(treeData);
    const root = tree(data);
    console.log('root', root);
    const nodes = root.descendants();
    console.log('nodes', nodes);

    nodeG.selectAll('.node')
      .data(data)
      .enter()
      .append('rect')
      .attr('class', 'node')
      .attr('width', nodeStyle.width)
      .attr('height', nodeStyle.height)
      .attr('rx', nodeStyle.r)
      .attr('ry', nodeStyle.r)
      .attr('fill', 'lightgreen')
      .attr('stroke', 'red')
      .attr('stroke-width', '1px')
      .text((d: any) => d.data.name)
      .attr('x', (a: any) => a.x)
      .attr('y', (a: any) => a.y)
  }

  onMounted(() => {
    console.log('onMounted TreeView');
    Draw();
  });
</script>
<style scoped lang="scss">
  .TreeView {
    width: 100vw;
    height: 100vh;
  }
</style>
