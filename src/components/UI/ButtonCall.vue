<template>
  <button class="button-call"><span class="button-call_inner"></span></button>
</template>

<script>
export default {
  name: "ButtonCall",
};
</script>

<style scoped>
.button-call {
  padding: 5px;
  border: 1px solid #696969;
  border-radius: 5px;
  cursor: pointer;
}

.button-call:disabled {
  border-color: #e52828;
}

.button-call_inner {
  position: relative;
  display: block;
  width: 15px;
  height: 15px;
  border: 1px solid #696969;
  border-radius: 50%;
}

.button-call:disabled .button-call_inner {
  background-color: #e52828;
  border-color: inherit;
}
</style>
