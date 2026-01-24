<script setup>
import supervisioncpu from './components/supervision_cpu.vue';
import supervisionmemoire from './components/supervision_memoire.vue';
import supervisionreseau from './components/supervision_network.vue';
</script>

<template>
  <div>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
      <h1>Supervision Raspberry</h1>
    </a>
  </div>
  <supervisioncpu/>
  <supervisionmemoire/>
  <supervisionreseau/>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
