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

  const route = useRoute();
  const router = useRouter();
  const query = route.query;

  const TreeView = ref<HTMLDivElement | null>(null);


  function Draw() {
    //节点的样式
    const nodeStyle = {
      width: 200,
      height: 60,
      background: 'green',
      r: 8,
      color: 'white'
    };
    const rootNodeBgColor = 'orange';
    const subNodeBgColor = 'blue';
    const animationDuration = 100;

    //创建svg
    const $svg = d3.create('svg');
    $svg.attr('width', '100%');
    $svg.attr('height', '100%');
    $svg.attr('viewBox', '-500 -500 1000 1000');

    const $wrap = $svg.append('g');
    const zoom = d3.zoom().on('zoom', ev => {
      $wrap.attr('transform', ev.transform);
    });
    // @ts-ignore
    $svg.call(zoom);
    const $linkGroup = $wrap.append('g').attr('class', 'link-group');
    const $nodeGroup = $wrap.append('g').attr('class', 'node-group');

    TreeView.value!.appendChild($svg.node() as SVGElement);

    /* tree */

    const tree = d3.tree().nodeSize([nodeStyle.height, nodeStyle.width]);
    const data = d3.hierarchy(treeData);

    /* draw */

    /**
     * @name 绘制
     * @param {Boolean} init 第一次
     */
    function draw(init = false) {
      let root = tree(data);

      let nodes = root.descendants();
      let left = root.children!.filter((a: any) => /^(1|2)/.test(a.data.name));
      let right = root.children!.filter((a: any) => /^(3|4)/.test(a.data.name));

      nodes.forEach(a => ([a.x, a.y] = [a.y, a.x])); // 需要旋转90度

      let leftMiddleOffset = (left[0].y + left[1].y) / 2
      left.forEach(a => {
        a.descendants().forEach(b => {
          b.x = -b.x;
          b.y -= leftMiddleOffset;
        });
      });
      let rightMiddleOffset = (right[0].y + right[1].y) / 2
      right.forEach(a => {
        a.descendants().forEach(b => {
          b.y -= rightMiddleOffset
        })
      });
      let $nodes = $nodeGroup
        .selectAll('.node')
        .data(nodes, (d: any) => d.data.name)
        .join(
          enter => {
            let $gs = enter.append('g');
            $gs.append('rect')
              .attr('width', nodeStyle.width / 2)
              .attr('height', nodeStyle.height * 0.66)
              .attr('transform', `translate(${-nodeStyle.width / 4}, ${-nodeStyle.height * 0.33})`)
              .attr('fill', (d: any) => {
                if (d.depth === 0) {
                  return rootNodeBgColor;
                } else if (d.children || d._children) {
                  return subNodeBgColor;
                } else {
                  return nodeStyle.background;
                }
              })
              .attr('rx', nodeStyle.r)
              .attr('ry', nodeStyle.r)
            $gs.append('text')
              .text((d: any) => d.data.name)
              .style('font-size', '20px')
              .attr('fill', nodeStyle.color)
              .attr('text-anchor', 'middle')
              .attr('y', nodeStyle.height * 0.16)

            return $gs
          },
          update => update,
          exit => {
            exit
              .transition()
              .duration(init ? 0 : animationDuration)
              .attr('opacity', 0)
              .attr('transform', (d: any) => `translate(${d.parent.x},${d.parent.y})`)
              .remove()
          }
        )
        .attr('class', 'node')
        .on('click', handle_node_click)
      $nodes
        .filter((a: any) => a.originX !== undefined && a.originY !== undefined)
        .attr('opacity', 0)
        .attr('transform', (d: any) => {
          let x, y

          if (d.originX) {
            x = d.originX
            delete d.originX
          } else {
            x = d.x
          }
          if (d.originY) {
            y = d.originY
            delete d.originY
          } else {
            y = d.y
          }
          return `translate(${x}, ${y})`
        })
      $nodes
        .transition()
        .duration(init ? 0 : animationDuration)
        .attr('opacity', 1)
        .attr('transform', d => `translate(${d.x}, ${d.y})`)
      let links = root.links()
      $linkGroup
        .selectAll('.link')
        .data(links, (d: any) => d.target.data.name)
        .join(
          enter =>
            enter
              .append('path')
              .attr('class', 'link')
              .attr('fill', 'none')
              .attr('stroke', 'gray')
              .attr('d', d => {
                let s: any = d.source;
                let origin = `${s.sourceX || s.x},${s.sourceY || s.y}`;
                return `M ${origin} L ${origin} L ${origin} L ${origin}`;
              }),
          update => update,
          exit =>
            exit
              .transition()
              .duration(init ? 0 : animationDuration)
              .attr('d', d => {
                let s = d.source
                let origin = `${s.x},${s.y}`

                return `M ${origin} L ${origin} L ${origin} L ${origin}`
              })
              .remove()
        )
        .transition()
        .duration(init ? 0 : animationDuration)
        .attr('d', d => {
          let s = d.source
          let t = d.target
          let mx = (s.x + t.x) / 2
          return `M ${s.x},${s.y} L ${mx},${s.y} L ${mx},${t.y} L ${t.x},${t.y}`
        })
    }
    /**
     * @name 处理结点点击
     * @param {Object} ev 事件
     * @param {Object} d 数据
     */
    function handle_node_click(ev: PointerEvent, d: any) {
      if (d.depth !== 0) {
        if (d.children) {
          d._children = d.children;
          d.children = undefined;
          draw();
        } else if (d._children) {
          for (let a of d._children) {
            a.originX = a.parent.x;
            a.originY = a.parent.y;
          }
          d.children = d._children;
          draw();
        }
      }
    }

    draw(true);
  }

  onMounted(() => {
    console.log('onMounted TreeView', TreeView);
    Draw();
  });
</script>
<style scoped lang="scss">
  .TreeView {
    /* 圈圈节点样式 */
    .node circle {
      fill: #fff;
      stroke: steelblue;
      stroke-width: 1.5px;
    }
    /* 文字样式 */
    .node text {
      font-size: 12px;
    }
    /* 连线的样式 */
    .link {
      fill: none;
      stroke: #ccc;
      stroke-width: 1.5px;
    }
  }
</style>
