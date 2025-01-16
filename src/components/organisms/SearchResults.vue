<script setup lang="ts">
const props = defineProps({
	searchResults: {
		type: Object,
	},
});
</script>

<template>
	<div>
		<ul class="search-results-list">
			<template v-if="searchResults.repositories.length">
				<p class="results-title">Repositories found:</p>
				<li v-for="searchResult in searchResults.repositories" :key="searchResult.id">
					<a :href="searchResult.url">{{ searchResult.title }}</a>
				</li>
			</template>
			<!-- <p v-else>No matching repositories found...</p> -->
			<template v-if="searchResults.users.length">
				<p class="results-title">Users found:</p>
				<li class="search-result-wrapper" v-for="searchResult in searchResults.users" :key="searchResult.id">
					<div class="user-basic-data-wrapper">
						<div class="user-avatar-wrapper">
							<img class="user-avatar-img" :src="searchResult.avatarUrl" alt="" />
						</div>
						<a :href="searchResult.url">{{ searchResult.name }}</a>
					</div>
				</li>
			</template>
			<!-- <p v-else>No matching users found...</p> -->
		</ul>
	</div>
</template>

<style lang="scss" scoped>
.search-results-list {
	display: flex;
	flex-direction: column;
	gap: 0.6rem;
	padding: 1.6rem 0;
	list-style: none;
}

.search-result-wrapper {
	padding: 0.6rem;
	border: 1px solid rgb(209, 217, 224);
	border-radius: 6px;
}

.user-avatar-wrapper {
	border-radius: 50%;
	box-shadow: 0 0 0 1px #1f232826;
	width: 30px;
	aspect-ratio: 1 / 1;
	overflow: hidden;
}

.user-avatar-img {
	width: 100%;
	object-fit: cover;
}

.user-basic-data-wrapper {
	display: flex;
	align-items: center;
	gap: 1.2rem;
}
</style>
