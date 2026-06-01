<script setup>
  import projects from '../data/projects.json';
  import ProjectCard from './ProjectCard.vue';
</script>

<template>
  <div id="projects" class="container-fluid section-pad bg-light">
    <h2 class="section-title text-center">My Projects</h2>
    <p class="section-subtitle text-center">Here's what I have built</p>
    <div class="row row-cols-1 row-cols-md-2 row-cols-lg-4 g-4 mt-2">
      <ProjectCard
        v-for="project in projects"
        :key="project.id"
        :project="project"
      />
    </div>
  </div>
</template>