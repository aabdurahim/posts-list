<template>
  <div>
    <NuxtLayout>
      <NuxtPage :transition="false" />
    </NuxtLayout>
  </div>
</template>

<script setup>
useHead({
  htmlAttrs: {
    lang: 'en',
  },
  meta: [
    { name: 'description', content: 'Posts List Test' }
  ],
  title: 'Posts List'
})
</script>