<template>
  <div class="elevator-shaft" ref="shaft">
    <elevator-cabin
      :style="{ height: floorHeight + '%' }"
      :elevator-item="elevator"
      :floor-height="floorHeight"
    />
  </div>
</template>

<script>
import ElevatorCabin from "@/components/ElevatorCabin";
export default {
  name: "LiftShaft",
  components: { ElevatorCabin },
  props: {
    floorHeight: {
      type: Number,
      require: true,
    },
    elevator: {
      type: Object,
      require: true,
    },
  },
};
</script>

<style scoped>
.elevator-shaft {
  position: relative;
  flex-basis: 100px;
  min-width: 100px;
  border-width: 0 2px;
  border-style: solid;
  border-color: #242424;
}
</style>
