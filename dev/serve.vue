<script>
import { defineComponent } from 'vue';
import NepalMapBasins from '@/nepal-map-basins.vue';

export default defineComponent({
  name: 'ServeDev',
  components: {
    NepalMapBasins
  }
});
</script>

<template>
  <div id="app">
    <nepal-map-basins />
  </div>
</template>
