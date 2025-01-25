<template>
  <section class="bg-green-50">
    <div class="container m-auto max-w-2xl py-24">
      <div
        class="bg-white px-6 py-8 mb-4 shadow-md rounded-md border m-4 md:m-0"
      >
        <form @submit.prevent="handleSubmit">
          <h2 class="text-3xl text-center font-semibold mb-6">编辑招聘信息</h2>

          <div class="mb-4">
            <label for="type" class="block text-gray-700 font-bold mb-2"
              >工作类型</label
            >
            <select
              v-model="form.type"
              id="type"
              name="type"
              class="border rounded w-full py-2 px-3"
              required
            >
              <option value="全职">全职</option>
              <option value="零工">零工</option>
              <option value="远程办公">远程办公</option>
              <option value="实习">实习</option>
            </select>
          </div>

          <div class="mb-4">
            <label class="block text-gray-700 font-bold mb-2">岗位名称</label>
            <input
              v-model="form.title"
              type="text"
              id="name"
              name="name"
              class="border rounded w-full py-2 px-3 mb-2"
              required
            />
          </div>
          <div class="mb-4">
            <label for="description" class="block text-gray-700 font-bold mb-2"
              >岗位描述</label
            >
            <textarea
              v-model="form.description"
              id="description"
              name="description"
              class="border rounded w-full py-2 px-3"
              rows="4"
            ></textarea>
          </div>

          <div class="mb-4">
            <label for="type" class="block text-gray-700 font-bold mb-2"
              >薪资</label
            >
            <input
              v-model="form.salary"
              type="text"
              id="salary"
              name="salary"
              class="border rounded w-full py-2 px-3 mb-2"
              required
            >
            </input>
          </div>

          <div class="mb-4">
            <label class="block text-gray-700 font-bold mb-2"> 地点 </label>
            <input
              v-model="form.location"
              type="text"
              id="location"
              name="location"
              class="border rounded w-full py-2 px-3 mb-2"
              required
            />
          </div>

          <div class="mb-4">
            <label for="company" class="block text-gray-700 font-bold mb-2"
              >公司</label
            >
            <select
              v-model="form.company"
              id="company"
              name="company"
              class="border rounded w-full py-2 px-3 bg-gray-100"
              disabled
            >
          <option :value="form.company">{{ form.company.name }}</option>
        </select>
          </div>

          <div>
            <button
              class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline"
              type="submit"
            >
              完成编辑
            </button>
          </div>
        </form>
      </div>
    </div>
  </section>
</template>

<script setup>
import { reactive, onMounted } from 'vue';
import { useToast } from 'vue-toastification';
import router from '@/router';
import { useRoute } from 'vue-router';
import supabase from '@/config/supabaseClient';
const route = useRoute();

const jobId = route.params.id;

const state = reactive({
  job: {},
  isLoading: true,
});

const form = reactive({
  type: '',
  title: '',
  description: '',
  salary: '',
  location: '',
  company: {},
});

const toast = useToast();

const handleSubmit = async () => {
  const updatedJob = {
    title: form.title,
    type: form.type,
    location: form.location,
    description: form.description,
    salary: form.salary,
    company_id: form.company.id
  };
  try {
    const {error} = await supabase.from('job').update(updatedJob).eq('id', jobId);
    if(error){
      throw new Error(error.code);
    }
    toast.success('岗位编辑成功');
    router.push(`/jobs/${jobId}`);
  } catch (error) {
    console.error('Error fetching', error);
    toast.error('岗位编辑失败');
  }
};

onMounted(async () => {
  try {
    const { data, error } = await supabase
      .from('job')
      .select(
        `id,title,type,description,location,salary,company(id,name)`
      )
      .eq('id', jobId)
      .single();
    if(error){
      throw new Error(error.code);
    }
    state.job = data;
    form.type = state.job.type;
    form.title = state.job.title;
    form.description = state.job.description;
    form.salary = state.job.salary;
    form.location = state.job.location;
    form.company = state.job.company;
    
  } catch (error) {
    console.error('Error fetching job', error);
  } finally {
    state.isLoading = false;
  }
});
</script>
