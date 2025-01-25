<template>
  <BackButton />
  <section v-if="!state.isLoading" class="bg-green-50">
    <div class="container m-auto py-10 px-6">
      <div class="grid grid-cols-1 md:grid-cols-70/30 w-full gap-6">
        <main>
          <div
            class="bg-white p-6 rounded-lg shadow-md text-center md:text-left"
          >
            <div class="text-gray-500 mb-4">{{ state.job.type }}</div>
            <h1 class="text-3xl font-bold mb-4">{{ state.job.title }}</h1>
            <div
              class="text-gray-500 mb-4 flex align-middle justify-center md:justify-start"
            >
              <i class="pi pi-map-marker text-orange-700 mr-2 text-lg"></i>
              <p class="text-orange-700">{{ state.job.location }}</p>
            </div>
          </div>

          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <h3 class="text-green-800 text-lg font-bold mb-6">岗位描述</h3>

            <p class="mb-4">
              {{ state.job.description }}
            </p>

            <h3 class="text-green-800 text-lg font-bold mb-2">薪资</h3>

            <p class="mb-4">{{ state.job.salary }}</p>
          </div>
        </main>

        <!-- Sidebar -->
        <aside>
          <!-- Company Info -->
          <div class="bg-white p-6 rounded-lg shadow-md">
            <h3 class="text-xl font-bold mb-6">公司信息</h3>

            <h2 class="text-2xl">{{ state.job.company.name }}</h2>

            <p class="my-2">
              {{ state.job.company.description }}
            </p>

            <hr class="my-4" />

            <h3 class="text-xl">邮箱:</h3>

            <p class="my-2 bg-green-100 p-2 font-bold">
              {{ state.job.company.contactEmail }}
            </p>

            <h3 class="text-xl">联系电话:</h3>

            <p class="my-2 bg-green-100 p-2 font-bold">
              {{ state.job.company.contactPhone }}
            </p>
          </div>

          <!-- Manage -->
          <div class="bg-white p-6 rounded-lg shadow-md mt-6">
            <RouterLink
              :to="`/jobs/edit/${state.job.id}`"
              class="bg-green-500 hover:bg-green-600 text-white text-center font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline block"
              >编辑</RouterLink
            >
            <button
              @click="deleteJob"
              class="bg-red-500 hover:bg-red-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline mt-4 block"
            >
              删除
            </button>
          </div>
        </aside>
      </div>
    </div>
  </section>
  <div v-else class="text-center text-gray-500 py-6">
    <PulseLoader />
  </div>
</template>

<script setup>
import PulseLoader from 'vue-spinner/src/PulseLoader.vue';
import { useRoute, RouterLink, useRouter } from 'vue-router';
import { reactive, onMounted } from 'vue';
import BackButton from '@/components/BackButton.vue';
import { useToast } from 'vue-toastification';
import supabase from '@/config/supabaseClient';
const route = useRoute();
const router = useRouter();
const toast = useToast();
const jobId = route.params.id;

const state = reactive({
  job: {},
  isLoading: true,
});

const deleteJob = async () => {
  try {
    const confirm = window.confirm('Are you sure you want to delete this?');
    if (confirm) {
      const { error } = await supabase.from('job').delete().eq('id', jobId);
      toast.success('岗位删除成功');
      if (error) {
        throw new Error(error.code);
      }
      router.push('/jobs');
    }
  } catch (error) {
    console.error('Error deleting job', error);
    toast.error('岗位删除失败');
  }
};
onMounted(async () => {
  try {
    const { data, error } = await supabase
      .from('job')
      .select(
        `id,title,type,description,location,salary,company(name,description,contactEmail,contactPhone)`
      )
      .eq('id', jobId)
      .single();
    console.log(data);
    if (error) {
      throw new Error(error.code);
    }
    state.job = data;
  } catch (error) {
    console.error('Error fetching job', error);
  } finally {
    state.isLoading = false;
  }
});
</script>
