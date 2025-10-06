<script setup>
import { ref, onMounted, onUnmounted } from "vue";
import PropertyCard from "./components/PropertyCard.vue";

const properties = ref([]);
const loading = ref(true);
const error = ref(null);
const infiniteLoading = ref(false);
const page = ref(1);
const newlyAddedCards = ref(new Set());

const fetchGitHubData = async (pageNum = 1, append = false) => {
	try {
		if (!append) loading.value = true;

		// Fetch popular Vue.js repositories from GitHub API
		const response = await fetch(
			`https://api.github.com/search/repositories?q=topic:vue+stars:>1000&sort=stars&order=desc&per_page=12&page=${pageNum}`
		);

		if (!response.ok) {
			throw new Error("Failed to fetch data");
		}

		const data = await response.json();

		// Transform GitHub repo data into property cards
		const newProperties = data.items.map((repo, index) => ({
			id: `${repo.id}-${pageNum}`, // Make ID unique for each page
			title: "Iceland Cabin",
			price: Math.floor(Math.random() * 500) + 200, // Random price between 200-700
			description: `Cozy cabin in the heart of Iceland. Enjoy stunning views of the Northern Lights and explore nearby glaciers and hot springs.`,
			rating:
				repo.stargazers_count > 10000
					? "Top Rated"
					: repo.stargazers_count > 5000
					? "Highly Rated"
					: "Featured",
			duration: `${Math.floor(Math.random() * 5) + 2} Day stay`,
			image: `https://picsum.photos/seed/${repo.id + pageNum}/600/400`,
			stars: repo.stargazers_count,
			language: repo.language,
			owner: repo.owner.login,
		}));

		if (append) {
			// Mark new cards for animation
			const newCardIds = new Set(newProperties.map((p) => p.id));
			newlyAddedCards.value = newCardIds;

			properties.value = [...properties.value, ...newProperties];

			// Clear the newly added cards set after animation duration
			setTimeout(() => {
				newlyAddedCards.value.clear();
			}, 1000);
		} else {
			properties.value = newProperties;
		}

		loading.value = false;
	} catch (err) {
		error.value = err.message;
		loading.value = false;
		console.error("Error fetching GitHub data:", err);
	}
};

const loadMoreProperties = async () => {
	if (infiniteLoading.value) return;

	infiniteLoading.value = true;

	await new Promise((resolve) => setTimeout(resolve, 2000));

	page.value += 1;
	await fetchGitHubData(page.value, true);
	infiniteLoading.value = false;
};

const handleScroll = () => {
	const { scrollTop, scrollHeight, clientHeight } = document.documentElement;

	// Check if user has scrolled to near the bottom (within 100px)
	if (
		scrollTop + clientHeight >= scrollHeight - 100 &&
		!infiniteLoading.value &&
		!loading.value
	) {
		loadMoreProperties();
	}
};

onMounted(() => {
	fetchGitHubData();
	window.addEventListener("scroll", handleScroll);
});

onUnmounted(() => {
	window.removeEventListener("scroll", handleScroll);
});
</script>

<template>
	<div class="min-h-screen bg-gray-50 py-8 px-4">
		<div class="max-w-7xl mx-auto">
			<!-- Header -->
			<div class="text-center mb-12">
				<h1 class="text-4xl font-bold text-gray-900 mb-4">
					Travel Locations
				</h1>
			</div>

			<!-- Loading State -->
			<div v-if="loading" class="text-center py-12">
				<div
					class="inline-block animate-spin rounded-full h-12 w-12 border-b-2 border-gray-900"
				></div>
				<p class="mt-4 text-gray-600">Loading amazing locations...</p>
			</div>

			<!-- Error State -->
			<div v-else-if="error" class="text-center py-12">
				<div
					class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded mx-auto max-w-md"
				>
					<p class="font-bold">Error loading data</p>
					<p class="text-sm">{{ error }}</p>
				</div>
			</div>

			<!-- Property Grid -->
			<div
				v-else
				class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-8"
			>
				<PropertyCard
					v-for="(property, index) in properties"
					:key="property.id"
					:property="property"
					:class="{
						'property-card-animate': newlyAddedCards.has(property.id),
						'property-card-static': !newlyAddedCards.has(property.id),
					}"
					:style="
						newlyAddedCards.has(property.id)
							? `animation-delay: ${(index % 12) * 0.1}s`
							: ''
					"
				/>
			</div>

			<div v-if="infiniteLoading" class="text-center py-8">
				<div
					class="inline-block animate-spin rounded-full h-8 w-8 border-b-2 border-gray-900"
				></div>
				<p class="mt-4 text-gray-600">Loading...</p>
			</div>
		</div>
	</div>
</template>

<style scoped>
.property-card-static {
	opacity: 1;
	transform: translateY(0);
}

.property-card-animate {
	opacity: 0;
	transform: translateY(50px);
	animation: fadeUpFromBottom 0.8s ease-out forwards;
}

@keyframes fadeUpFromBottom {
	0% {
		opacity: 0;
		transform: translateY(50px);
	}
	100% {
		opacity: 1;
		transform: translateY(0);
	}
}
</style>
