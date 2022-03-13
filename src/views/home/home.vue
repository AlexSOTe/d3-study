
<template>
  <div class="home">
    <TreeView />
  </div>
</template>

<script setup lang="ts">
  import { onMounted, reactive, ref, computed } from 'vue';
  import { useRouter, useRoute } from 'vue-router';
  import TreeView from '../../components/home/TreeView.vue';


  const router = useRouter();
  const route = useRoute();
  const query = route.query;

</script>

<style scoped lang="scss">
  .home {
  }
</style>
