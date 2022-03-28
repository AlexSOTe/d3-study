
<template>
  <div class="home">
    <Com01 />
  </div>
</template>

<script setup lang="ts">
  /**
   * 教程地址：https://www.bilibili.com/video/BV1HK411L72d?p=3
   */

  import { onMounted, reactive, ref, computed } from 'vue';
  import { useRouter, useRoute } from 'vue-router';


  const router = useRouter();
  const route = useRoute();
  const query = route.query;

</script>

<style scoped lang="scss">
  .home {
  }
</style>
