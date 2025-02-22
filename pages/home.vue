<template>
    <div class="relative min-h-screen text-white">
        <div class="relative flex flex-col justify-center items-center h-[calc(100vh-88.8px)] text-center">
            <h1 class="text-5xl font-bold leading-tight pb-4">
                Tell me about your product <br /> and let me run my magic
            </h1>
            <h3 class="text-lg text-gray-400 pb-4">Enter the link to your product</h3>
            <input v-model="productUrl" type="text" placeholder="https://example.com/product"
                class="w-115 border border-gray-700 bg-gray-900 p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 text-gray-300" />
            <button @click="extractKeywords"
                class="mt-4 bg-blue-600 hover:bg-blue-700 px-6 py-2 rounded-lg text-white font-semibold">
                Extract Keywords
            </button>
            <div v-if="loading" class="mt-4 text-gray-400">Loading...</div>
            <div v-if="error" class="mt-4 text-red-500">{{ error }}</div>
            <div v-if="keywords.length" class="m-4">
                <ul class="grid grid-cols-3 gap-4 p-4 text-black">
                    <li v-for="(keyword, index) in keywords" :key="index"
                        @click="toggleKeywordSelection(keyword)"
                        :class="{'bg-blue-500 text-white': selectedKeywords.includes(keyword), 'bg-white text-black': !selectedKeywords.includes(keyword)}"
                        class="cursor-pointer rounded-lg py-2 font-semibold px-4 transition">
                        {{ keyword }}
                    </li>
                </ul>
                <button @click="sendSelectedKeywords"
                    class="mt-4 bg-green-600 hover:bg-green-700 px-6 py-2 rounded-lg text-white font-semibold">
                    Submit Selected Keywords
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';

const router = useRouter();
const productUrl = ref('');
const keywords = ref([]);
const selectedKeywords = ref([]);
const loading = ref(false);
const error = ref(null);

const extractKeywords = async () => {
    if (!productUrl.value.trim()) return;
    
    loading.value = true;
    error.value = null;
    
    try {
        const response = await axios.post('https://e134-182-156-21-2.ngrok-free.app/extract-keywords', {
            url: productUrl.value
        });
        keywords.value = response.data.keywords || [];
    } catch (err) {
        error.value = 'Failed to fetch keywords. Please try again.';
    } finally {
        loading.value = false;
    }
};

const toggleKeywordSelection = (keyword) => {
    if (selectedKeywords.value.includes(keyword)) {
        selectedKeywords.value = selectedKeywords.value.filter(k => k !== keyword);
    } else {
        selectedKeywords.value.push(keyword);
    }
};

const sendSelectedKeywords = async () => {
    try {
        const response = await axios.post('http://localhost:5000/competitor_search', {
            keywords: selectedKeywords.value
        });
        
        router.push({ name: 'CompetitorResults', state: { competitors: response.data.competitors } });
    } catch (err) {
        console.error('Failed to send selected keywords:', err);
    }
};
</script>
