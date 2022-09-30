<template>
  <div class="buttons-group">
    <floor-panel
      v-for="(floor, idx) in floors"
      :key="idx"
      :floorNumber="idx + 1"
      :is-active="floor.isActiveButton"
    />
  </div>
</template>

<script>
import FloorPanel from "@/components/FloorPanel";
export default {
  name: "ButtonGroup",
  components: { FloorPanel },
  props: {
    floors: {
      type: Array,
      required: true,
    },
  },
};
</script>

<style scoped>
.buttons-group {
  display: flex;
  flex-direction: column-reverse;
}
</style>
