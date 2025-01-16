<script setup lang="ts">
import SearchInput from '@/components/atoms/SearchInput.vue';
import StyledButton from '@/components/atoms/StyledButton.vue';

const props = defineProps({
	handleSearchTargetButtonClick: {
		type: Function,
	},
	handleFormSubmit: {
		type: Function,
	},
	searchInputValue: {
		type: String,
	},
	handleInputChange: {
		type: Function,
	},
	searchTarget: {
		type: String,
	},
	clearSearchInput: {
		type: Function,
	}
});
</script>

<template>
	<form @submit.prevent="handleFormSubmit" class="search-form">
		<SearchInput :searchInputValue :handleInputChange :clearSearchInput />
		<div class="search-target-buttons-wrapper">
			<StyledButton
				class="left"
				data-search-target="repositories"
				:isActive="searchTarget === 'repositories'"
				@click="handleSearchTargetButtonClick"
				type="button"
				:aria-label="`set respositories as search target (current search target is: ${searchTarget})`"
				>Repositories</StyledButton
			>
			<StyledButton
				class="right"
				data-search-target="users"
				:isActive="searchTarget === 'users'"
				@click="handleSearchTargetButtonClick"
				type="button"
				:aria-label="`set respositories as search target (current search target is: ${searchTarget})`"
				>Users</StyledButton
			>
		</div>
	</form>
</template>

<style lang="scss" scoped>
.search-form {
	display: flex;
	flex-direction: column;
	gap: 0.8rem;
}

.search-target-buttons-wrapper {
	display: grid;
	grid-template-columns: repeat(2, 1fr);
}
</style>
