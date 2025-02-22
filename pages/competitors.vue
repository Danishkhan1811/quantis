<template>
    <div class="min-h-screen bg-gray-900 text-white p-4">
        <h1 class="text-3xl font-bold mb-6">Competitor Analysis</h1>
        <ul v-if="competitors.length" class="grid grid-cols-2 gap-4">
            <li v-for="competitor in competitors" :key="competitor.id"
                class="flex flex-col border border-gray-700 rounded-lg p-4 bg-gray-800">
                <h2 class="text-xl font-semibold">{{ competitor.title }}</h2>
                <p class="text-gray-400 text-sm mt-1">{{ competitor.summary }}</p>
                <a :href="competitor.url" target="_blank" class="text-blue-400 mt-2 hover:underline">
                    Visit Website
                </a>
            </li>
        </ul>
        <p v-else class="text-gray-400">Loading competitors...</p>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import { useRoute } from 'vue-router';

const competitors = ref([]);
const route = useRoute();

const fetchCompetitors = async () => {
    try {
        const response = await axios.post('http://localhost:5000/competitor_search', {
            keywords: route.query.keywords ? route.query.keywords.split(',') : []
        });

        competitors.value = response.data.competitors?.results.map(({ title, url, summary }) => ({
            title,
            url,
            summary
        })) || [];
    } catch (error) {
        console.error('Error fetching competitors:', error);
    }
};

onMounted(fetchCompetitors);
</script>

<style>
img {
    width: 120px;
    height: 120px;
}
</style>