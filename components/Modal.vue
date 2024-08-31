<template>
  <div v-if="visible" class="fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center z-50">
    <div class="bg-white p-6 rounded shadow-lg w-1/3">
      <h2 class="text-xl font-semibold mb-4">Create New Post</h2>
      <form @submit.prevent="submitPost">
        <div class="mb-4">
          <label for="title" class="block text-gray-700">Title</label>
          <input
            v-model="newPost.title"
            id="title"
            type="text"
            class="mt-1 block w-full border-gray-300 rounded"
            required
          />
        </div>
        <div class="mb-4">
          <label for="body" class="block text-gray-700">Body</label>
          <textarea
            v-model="newPost.body"
            id="body"
            class="mt-1 block w-full border-gray-300 rounded"
            rows="4"
            required
          ></textarea>
        </div>
        <div class="flex justify-end">
          <button
            type="button"
            @click="$emit('close')"
            class="px-4 py-2 bg-gray-300 text-white rounded mr-2"
          >
            Cancel
          </button>
          <button
            type="submit"
            class="px-4 py-2 bg-blue-500 text-white rounded"
          >
            Create
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';

export default defineComponent({
  props: {
    visible: {
      type: Boolean,
      required: true
    }
  },
  emits: ['close', 'postCreated'],
  setup(props, { emit }) {
    const newPost = ref({
      title: '',
      body: ''
    });

    const submitPost = async () => {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/posts', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(newPost.value)
        });
        if (!response.ok) {
          throw new Error(`Error: ${response.statusText}`);
        }
        const data = await response.json();
        emit('postCreated', data);
        emit('close');
      } catch (err) {
        console.error(err);
      }
    };

    return {
      newPost,
      submitPost
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