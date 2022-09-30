<template>
  <div class="elevator-cabine"></div>
</template>

<script>
export default {
  name: "ElevatorCabine",
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
