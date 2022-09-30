<template>
  <div class="main-container">
    <EmulatorPanel />
  </div>
</template>

<script>
import EmulatorPanel from "@/components/EmulatorPanel";
export default {
  name: "App",
  components: { EmulatorPanel },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.main-container {
  padding: 0 40px;
  width: 100%;
  min-width: 720px;
}
</style>
