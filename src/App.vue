<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'

// reactive state 
const loading = ref(false);
const list = ref([
  { background: 'rgb(233,32,38)', }
]);

function addColorsToList(num: number) {
  for (let i = 0; i < num; i++) {
    let newColor = {
      background: `rgb(${Math.floor(Math.random() * 256)},${Math.floor(Math.random() * 256)},${Math.floor(
        Math.random() * 256
      )})`,
    };
    list.value.push(newColor);
  }
  loading.value = false;
}

async function getList(num: number) {
  if (list.value.length > 50) {
    return;
  }
  loading.value = true;
  await new Promise(resolve => setTimeout(resolve, Math.random() * 5000));
  addColorsToList(num);
}

async function handleScroll() {
  const scrollTop = document.documentElement.scrollTop || document.body.scrollTop;
  const scrollHeight = document.documentElement.scrollHeight || document.body.scrollHeight;

  if (scrollHeight - scrollTop < scrollHeight / 2 && list.value.length < 50) {
    window.removeEventListener('scroll', handleScroll);
    await getList(10);
    window.addEventListener('scroll', handleScroll);
  }
}

onMounted(() => {
  getList(10);
  window.addEventListener('scroll', handleScroll);
});

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll);
});

</script>

<template>
  <div v-for="(color, index) in  list" :key="index" :style="{ background: color.background }" class="colorList">
    {{ index }}
  </div>
  <div v-if="loading" class="loading"></div>
</template>

<style scoped>
.colorList {
  width: 100%;
  height: 500px;
  text-align: center;
  font-size: 50px;
}

.loading {
  border: 16px solid #f3f3f3;
  border-radius: 50%;
  border-top: 16px solid #263f51;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
  margin: auto;
}

@-webkit-keyframes spin {
  0% { -webkit-transform: rotate(0deg); }
  100% { -webkit-transform: rotate(360deg); }
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
