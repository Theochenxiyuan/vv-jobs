<template>
  <section class="bg-blue-50 px-4 py-10">
    <div class="container-xl lg:container m-auto">
      <h2 class="text-3xl font-bold text-green-500 mb-6 text-center">
        浏览岗位
      </h2>
      <!-- Show loading spinner while loading is true-->
      <div v-if="state.isLoading" class="text-center text-gray-500 py-6">
        <PulseLoader />
      </div>
      <!-- Show job listing when done is false-->
      <div v-else class="grid grid-cols-1 gap-6 md:grid-cols-3">
        <JobListing
          v-for="job in state.jobs.slice(0, limit || state.jobs.length)"
          :key="job.id"
          :job="job"
        />
      </div>
    </div>
  </section>

  <section class="m-auto max-w-lg my-10 px-6" v-if="showButton">
    <RouterLink
      to="/jobs"
      class="block bg-black text-white text-center py-4 px-6 rounded-xl hover:bg-gray-700"
      >查看全部</RouterLink
    >
  </section>
</template>

<script setup>
import { reactive, defineProps, onMounted } from 'vue';
import JobListing from './JobListing.vue';
import PulseLoader from 'vue-spinner/src/PulseLoader.vue';
import supabase from '@/config/supabaseClient';

const state = reactive({
  jobs: [],
  isLoading: true,
});
defineProps({
  limit: Number,
  showButton: {
    type: Boolean,
    default: false,
  },
});
onMounted(async () => {
  try {
    const { data, error } = await supabase
      .from('job')
      .select(
        `id,title,type,description,location,salary,company(name,description,contactEmail,contactPhone)`
      );
    if (error) {
      throw new Error(error.name);
    }
    state.jobs = data;
  } catch (error) {
    console.error('Error fetching jobs', error);
  } finally {
    state.isLoading = false;
  }
});
</script>
