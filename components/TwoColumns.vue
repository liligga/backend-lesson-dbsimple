<template>
  <div class="col-content">
    <h3><slot name="header"></slot></h3>
    <div class="left"><slot name="left"></slot></div>
    <div class="right"><slot name="right"></slot></div>
  </div>
</template>

<script setup>

</script>

<style scoped>
.col-content {
  display: grid;
  grid-template-columns: 2fr 3fr;
  grid-template-rows: auto 1fr;
  gap: 1rem;
}
.header {
  grid-column: 1 / 3;
  grid-row: 1;
}
.left {
  grid-column: 1;
  grid-row: 2;
}
.right {
  grid-column: 2;
  grid-row: 2;
}
.left ul, .right ul {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 2rem;
}
</style>
