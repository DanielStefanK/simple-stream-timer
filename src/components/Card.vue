<template>
  <div class="card">
    <slot/>
  </div>
</template>

<style scoped>
  .card {
    background-color: white;
    padding: .5rem 1rem;
    -webkit-box-shadow: 14px 24px 24px 5px rgba(0,0,0,0.23);
    -moz-box-shadow: 14px 24px 24px 5px rgba(0,0,0,0.23);
    box-shadow: 14px 24px 24px 5px rgba(0,0,0,0.23);
  }
</style>