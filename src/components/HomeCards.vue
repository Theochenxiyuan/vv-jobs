<template>
  <section class="py-4">
    <div class="container-xl lg:container m-auto">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4 p-4 rounded-lg">
        <Card>
          <h2 class="text-2xl font-bold">如果你是开发者</h2>
          <p class="mt-2 mb-4">快来看看有哪些适合你的岗位！</p>
          <RouterLink
            to="/jobs"
            class="inline-block bg-black text-white rounded-lg px-4 py-2 hover:bg-gray-700"
          >
            浏览全部岗位
          </RouterLink>
        </Card>
        <Card bg="bg-green-100">
          <h2 class="text-2xl font-bold">如果你在招人</h2>
          <p class="mt-2 mb-4">发布你的招聘信息吧！</p>
          <RouterLink
            to="/jobs/add"
            class="inline-block bg-green-500 text-white rounded-lg px-4 py-2 hover:bg-green-600"
          >
            发布招聘
          </RouterLink>
        </Card>
      </div>
    </div>
  </section>
</template>

<script setup>
import Card from './Card.vue';
import { RouterLink } from 'vue-router';
</script>
