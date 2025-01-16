<script setup lang="ts">
import Header from '@/components/organisms/Header.vue';
import SearchResults from '@/components/organisms/SearchResults.vue';

// import { Octokit, App } from "octokit";
import { Octokit } from '@octokit/core';
import { ref } from 'vue';

const initialSearchTarget = 'repositories';
const initialSearchResultsState = { repositories: [], users: [] };

const API_TOKEN = import.meta.env.VITE_GH_TOKEN;
const isLoading = ref(false);
const searchInputValue = ref('compact-cars');
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
	isLoading.value = true;
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
	isLoading.value = false;
};

const getCommits = async (commitsUrl: string) => {
	try {
		const response = await octokit.request({
			method: 'GET',
			url: commitsUrl,
			headers: {
				authorization: API_TOKEN,
			},
		});

		const commitsDataArr = response.data.map(item => ({
			message: item.commit.message,
			date: item.commit.author.date,
			author: item.commit.author.name,
		}));

		return commitsDataArr;
	} catch (error) {
		console.log(error);
	}
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
		const repositoryData = await Promise.all(
			response.data.items.map(async resultItem => {
				const commits = resultItem.size > 0 ? await getCommits(resultItem.commits_url) : [];

				return {
					id: resultItem.id,
					title: resultItem.full_name,
					url: resultItem.html_url,
					commits,
				};
			})
		);

		handleSearchResults('repositories', repositoryData);
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
		<p v-if="isLoading">Loading...</p>
		<SearchResults v-else :searchResults />
	</div>
</template>

<style scoped>
.app-wrapper {
	padding: 0 0.8rem;
}
</style>
