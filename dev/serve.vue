<script>
import { defineComponent } from "vue";
import GeoNepalBasins from "@/geo-nepal-basins.vue";

export default defineComponent({
  name: "ServeDev",
  components: {
    GeoNepalBasins,
  },
});
</script>

<template>
  <div id="app">
    <GeoNepalBasins :height="50" :width="50" />
  </div>
</template>
