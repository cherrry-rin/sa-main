<template>
  <div class="home-page">
    <SubnetCalculator />
  </div>
</template>

<script setup lang="ts">
import SubnetCalculator from '../components/SubnetCalculator.vue'
</script>

<style>
.home-page {
  min-height: 100vh;
  background-color: var(--background-primary);
  padding: 1rem;
}
</style>