<script setup lang="ts">
import SearchForm from '@/components/molecules/SearchForm.vue';

const props = defineProps({
	handleFormSubmit: {
		type: Function,
	},
});
</script>

<template>
	<header class="header">
		<p class="app-title">RepoScout - Github API test task</p>
		<SearchForm :handleFormSubmit />
	</header>
</template>

<style lang="scss" scoped>
.header {
	display: flex;
	flex-direction: column;
}

.app-title {
	padding: 0.6rem;
	font-weight: 500;
	font-size: 1.7rem;
	text-align: center;
}

@media (min-width: 1200px) {
	.app-title {
		padding: 0.8rem;
		font-size: 1.9rem;
	}
}
</style>
