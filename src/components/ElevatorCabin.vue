<template>
  <div
    class="elevator-cabine"
    :class="{
      wait: elevatorItem.status === 'wait',
    }"
    :style="liftStyle"
  >
    <div class="elevator-cabine__info" v-if="elevatorItem.status === 'move'">
      {{ direction }} {{ elevatorItem.endFloorNumber }}
    </div>
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
        ? "🠕"
        : "🠗";
    },
    position() {
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
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  transition-property: bottom;
  transition-timing-function: linear;
  background-color: cornflowerblue;
}

.wait {
  animation-name: blink;
  animation-timing-function: linear;
  animation-duration: 1s;
  animation-iteration-count: infinite;
}

.elevator-cabine__info {
  position: absolute;
  left: 50%;
  margin-top: 5px;
  margin-right: -50%;
  padding: 5px 15px;
  border-radius: 5px;
  transform: translateX(-50%);
  background-color: #545454;
  color: #fff;
}

@keyframes blink {
  50% {
    opacity: 0.5;
  }
}
</style>
