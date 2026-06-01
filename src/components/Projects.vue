<script setup>

	import projects from '../data/projects.json';
	import ProjectCard from './ProjectCard.vue';

	// computed() is used to create a value that is automatically updated whenever the data it depends on changes.
	import {computed} from 'vue';

	const chunkSize = 3;
	const chunkedProjects = computed(() => {

		const chunks = [];

		for (let i = 0; i < projects.length; i+= chunkSize) {

			chunks.push(projects.slice(i, i + chunkSize));
			console.log(chunks);

		}

		return chunks;
	})
	
</script>

<template>
	<!-- Start of My Projects -->
	<section class="pb-5" id="projects">
	    <h1 class="mt-5 mb-5 pb-4 text-center">My Projects</h1>

	    <div
	    	v-for="(group, index) in chunkedProjects"
	    	:key="index"
	    	class="card-deck my-5 justify-content-center"
	    >
	    	
	    	<ProjectCard
	    		v-for="project in group"
	    		:key="project.id"
	    		:project="project"
	    	 />
	    </div>
	    
	</section>
	<!-- End of My Projects -->
</template>

<style scoped>
	
</style>