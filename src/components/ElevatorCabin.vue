<template>
  <div
    class="elevator-cabine"
    :class="{
      wait: elevatorItem.status === 'wait',
    }"
    :style="liftStyle"
  >
    {{ position }}
    <h1 v-if="elevatorItem.status !== 'free'">
      {{ direction }} {{ elevatorItem.endFloorNumber }}
    </h1>
  </div>
</template>

<script>
export default {
  name: "ElevatorCabine",
  props: {
    elevatorItem: {
      type: Object,
      required: true,
    },
    floorHeight: {
      type: Number,
    },
  },
  computed: {
    direction() {
      return this.elevatorItem.endFloorNumber >
        this.elevatorItem.startFloorNumber
        ? "🡅"
        : "🡇";
    },
    position() {
      // const { endFloorNumber } = this.elevatorItem;
      return (this.elevatorItem.endFloorNumber - 1) * this.floorHeight;
    },
    moveTime() {
      const { startFloorNumber, endFloorNumber, speed } = this.elevatorItem;
      return Math.abs(startFloorNumber - endFloorNumber) * speed;
    },
    liftStyle() {
      return {
        transitionDuration: `${this.moveTime}ms`,
        bottom: `${this.position}%`,
      };
    },
  },
};
</script>

<style scoped>
.elevator-cabine {
  background-color: cornflowerblue;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  transition-property: bottom;
  transition-timing-function: linear;
}

.wait {
  opacity: 0.5;
}
</style>
