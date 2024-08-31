<template>
  <section>
    <div class="container mx-auto mt-10 px-3">
      <h1 class="text-3xl text-primary font-bold uppercase">List of posts</h1>
      <div class="mt-5">
        <button @click="sortById" class="mb-4 px-4 py-2 bg-blue-500 text-white rounded">
          Sort by id ({{ isAscending ? 'ascending' : 'descending' }})
        </button>
        <button @click="showModal = true" class="mb-4 ml-3 px-4 py-2 bg-green-500 text-white rounded">
          Create New Post
        </button>
        <div v-if="loading" class="text-gray-500">
          <img src="/icons/loading.svg" class="w-20" alt="">
        </div>
        <div v-else-if="error" class="text-red-500">{{ error }}</div>
        <div v-else>
          <div v-if="posts.length > 0">
            <div v-for="post in paginatedPosts" :key="post.id" class="mt-8 lg:w-1/3">
              <h2 class="text-xl font-semibold">{{ post.title }}</h2>
              <p class="mt-2 text-gray-500">{{ post.body }}</p>
            </div>
            <div class="mt-5 flex justify-between">
              <button
                @click="prevPage"
                :disabled="currentPage === 1"
                class="px-4 py-2 bg-gray-300 text-white rounded"
              >
                Previous
              </button>
              <span class="px-4 py-2">{{ currentPage }} / {{ totalPages }}</span>
              <button
                @click="nextPage"
                :disabled="currentPage === totalPages"
                class="px-4 py-2 bg-gray-300 text-white rounded"
              >
                Next
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Modal :visible="showModal" @close="showModal = false" @postCreated="addPost" />
  </section>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, computed } from 'vue';

interface Post {
  id: number;
  title: string;
  body: string;
}

export default defineComponent({
  setup() {
    const posts = ref<Post[]>([]);
    const loading = ref(false);
    const error = ref<string | null>(null);
    const isAscending = ref(true);
    const currentPage = ref(1);
    const postsPerPage = 10;
    const showModal = ref(false);

    const fetchPosts = async () => {
      loading.value = true;
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts');
        if (!response.ok) {
          throw new Error(`Error: ${response.statusText}`);
        }
        const data: Post[] = await response.json();
        posts.value = data;
      } catch (err: any) {
        error.value = err.message || 'Ошибка загрузки данных';
      } finally {
        loading.value = false;
      }
    };

    const sortById = () => {
      posts.value.sort((a, b) => isAscending.value ? a.id - b.id : b.id - a.id);
      isAscending.value = !isAscending.value;
    };

    const paginatedPosts = computed(() => {
      const start = (currentPage.value - 1) * postsPerPage;
      const end = start + postsPerPage;
      return posts.value.slice(start, end);
    });

    const totalPages = computed(() => Math.ceil(posts.value.length / postsPerPage));

    const prevPage = () => {
      if (currentPage.value > 1) {
        currentPage.value--;
      }
    };

    const nextPage = () => {
      if (currentPage.value < totalPages.value) {
        currentPage.value++;
      }
    };

    const addPost = (newPost: Post) => {
      posts.value.unshift(newPost);
    };

    onMounted(() => {
      fetchPosts();
    });

    return {
      posts,
      loading,
      error,
      sortById,
      isAscending,
      paginatedPosts,
      totalPages,
      currentPage,
      prevPage,
      nextPage,
      showModal,
      addPost
    };
  }
});
</script>

<style scoped>
button {
  transition: background-color 0.3s ease;
}
button:hover {
  background-color: #DDDDDD;
}
</style>