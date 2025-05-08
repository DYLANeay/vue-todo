<template>
  <div ref="div">
    Temps : {{ time }}<br />
    Largeur : {{ size.width }}, Hauteur : {{ size.height }}
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';
const time = ref(0);

let timer;

const size = ref({ width: 0, height: 0 });

const div = ref(null);

onUnmounted(() => {
  // Cleanup the timer when the component is unmounted
  // This is important to prevent memory leaks
  clearInterval(timer);
});

onMounted(() => {
  const rect = div.value.getBoundingClientRect();
  size.value = {
    width: rect.width,
    height: rect.height,
  };

  timer = setInterval(() => {
    time.value++;
  }, 1000);
});
</script>
