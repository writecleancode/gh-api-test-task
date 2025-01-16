<script setup lang="ts">
import Header from '@/components/organisms/Header.vue';
import SearchResults from '@/components/organisms/SearchResults.vue';

// import { Octokit, App } from "octokit";
import { Octokit } from '@octokit/core';
import { ref } from 'vue';

const initialSearchTarget = 'repositories';
const initialSearchResultsState = { repositories: [], users: [] };

const API_TOKEN = import.meta.env.VITE_GH_TOKEN;
const searchInputValue = ref('writecleancode');
const searchTarget = ref(initialSearchTarget);
const searchResults = ref(initialSearchResultsState);

const octokit = new Octokit({ auth: API_TOKEN });

const handleInputChange = (e: Event) => {
	searchInputValue.value = (e.target as HTMLInputElement).value;
};

const clearSearchInput = () => (searchInputValue.value = '');

const handleSearchTargetButtonClick = e => {
	searchTarget.value = e.target.dataset.searchTarget;
};

const handleFormSubmit = () => {
	if (searchTarget.value === 'repositories') {
		getMatchingRepositories(searchInputValue.value);
	} else {
		getMatchingUsers(searchInputValue.value);
	}
};

const handleSearchResults = (resultsType, resultsArr) => {
	searchResults.value = {
		...initialSearchResultsState,
		[resultsType]: resultsArr,
	};
};

const getMatchingRepositories = async (searchPhrase: string) => {
	try {
		const response = await octokit.request({
			method: 'GET',
			url: `/search/repositories?q=${searchPhrase}`,
			headers: {
				authorization: API_TOKEN,
			},
		});
		console.log(response.data.items);
		const results = response.data.items.map(resultItem => ({
			id: resultItem.id,
			title: resultItem.full_name,
			url: resultItem.html_url,
		}));
		handleSearchResults('repositories', results);
	} catch (error) {
		console.log(error);
	}
};

const getMatchingUsers = async (searchPhrase: string) => {
	try {
		const response = await octokit.request({
			method: 'GET',
			url: `/search/users?q=${searchPhrase}`,
			headers: {
				authorization: API_TOKEN,
			},
		});
		console.log(response);
		const results = response.data.items.map(resultItem => ({
			id: resultItem.id,
			name: resultItem.login,
			profileUrl: resultItem.html_url,
			avatarUrl: resultItem.avatar_url,
		}));
		handleSearchResults('users', results);
	} catch (error) {
		console.log(error);
	}
};
</script>

<template>
	<div class="app-wrapper">
		<Header :handleSearchTargetButtonClick :searchTarget :handleFormSubmit :searchInputValue :handleInputChange :clearSearchInput />
		<SearchResults :searchResults />
	</div>
</template>

<style scoped>
.app-wrapper {
	padding: 0 0.8rem;
}
</style>
