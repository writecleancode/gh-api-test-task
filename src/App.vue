<script setup lang="ts">
// import { Octokit, App } from "octokit";
import { Octokit } from '@octokit/core';
import { ref, watch } from 'vue';

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
			url: resultItem.html_url,
			avatar: resultItem.avatar_url,
		}));
		handleSearchResults('users', results);
	} catch (error) {
		console.log(error);
	}
};

watch(searchResults, () => {
	console.log(searchResults.value);
});
</script>

<template>
	<p>RepoScout - Github API test task</p>
	<form @submit.prevent="handleFormSubmit">
		<div>
			<input type="text" :value="searchInputValue" @input="handleInputChange" />
			<button data-search-target="repositories" @click="handleSearchTargetButtonClick" type="button">Repositories</button>
			<button data-search-target="users" @click="handleSearchTargetButtonClick" type="button">Users</button>
		</div>
		<button type="submit">Make API request!</button>
	</form>
	<p>Results:</p>
	<ul>
		<template v-if="searchResults.repositories.length">
			<li v-for="searchResult in searchResults.repositories" :key="searchResult.id">
				<a :href="searchResult.url">{{ searchResult.title }}</a>
			</li>
		</template>
		<template v-if="searchResults.users.length">
			<li v-for="searchResult in searchResults.users" :key="searchResult.id">
				<a :href="searchResult.url">{{ searchResult.name }}</a>
			</li>
		</template>
	</ul>
</template>

<style scoped></style>
