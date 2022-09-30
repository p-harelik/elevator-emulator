<template>
  <div class="buttons-group">
    <floor-panel
      v-for="(floor, idx) in floors"
      :key="idx"
      :floorNumber="idx + 1"
      :is-active="floor.isActiveButton"
      @addCall="addCallOfElevator"
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
  methods: {
    addCallOfElevator(floorNumber) {
      this.$emit("addCall", floorNumber);
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
