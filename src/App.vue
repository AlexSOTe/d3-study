
<template>
  <div class="App">
    <div class="pages">
      <router-view v-slot="{ Component }">
        <keep-alive>
          <component :is="Component" v-if="route.meta.keepAlive" />
        </keep-alive>
        <component :is="Component" v-if="!route.meta.keepAlive" />
      </router-view>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { useRoute, useRouter } from "vue-router";
  import { onMounted, watch } from 'vue';
  import { store } from './store/index';

  const route = useRoute();

</script>

<style scoped>
  .App {
  }

  .navBar {
  }

  .pages {
  }
</style>
