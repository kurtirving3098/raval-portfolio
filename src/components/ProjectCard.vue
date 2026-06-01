<script setup>
  defineProps({
    project: Object
  })
</script>

<template>
  <div class="col">
    <div class="card h-100 project-card">
      <img :src="project.image" class="card-img-top project-img" :alt="project.title">
      <div class="card-body">
        <h5 class="card-title">{{ project.title }}</h5>
        <p class="card-text">{{ project.description }}</p>
      </div>
    </div>
  </div>
</template>