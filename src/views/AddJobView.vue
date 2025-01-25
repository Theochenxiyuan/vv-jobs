<template>
  <section class="bg-green-50">
    <div class="container m-auto max-w-2xl py-24">
      <div
        class="bg-white px-6 py-8 mb-4 shadow-md rounded-md border m-4 md:m-0"
      >
        <form @submit.prevent="handleSubmit">
          <h2 class="text-3xl text-center font-semibold mb-6">发布招聘</h2>

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
              class="border rounded w-full py-2 px-3"
            >
          <option v-for="company in state.companyOptions" :key="company.id" :value="company">{{ company.name }}</option>
        </select>
          </div>

          <div>
            <button
              class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline"
              type="submit"
            >
              发布
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
import supabase from '@/config/supabaseClient';

const form = reactive({
  type: '',
  title: '',
  description: '',
  salary: '',
  location: '',
  company: {},
});

const toast = useToast();
const state = reactive({
  companyOptions: [],
  isLoading: true
})
const handleSubmit = async () => {
  const newJob = {
    title: form.title,
    type: form.type,
    location: form.location,
    description: form.description,
    salary: form.salary,
    company_id: form.company.id,
  };

  try {
    const { data, error }  = await supabase.from('job').insert([
      newJob
    ]).select('id').single();
    if(error){
      throw new Error(error.code);
    }
    toast.success('发布岗位成功');
    router.push(`/jobs/${data.id}`);
  } catch (error) {
    console.error(error); 
    toast.error('岗位发布失败'); 
  }
};

onMounted(async ()=>{
  try {
    const { data, error } = await supabase
      .from('company')
      .select(
        `id,name`
      );
    if(error){
      throw new Error(error.code);
    }
    state.companyOptions = data;
  } catch (error) {
    console.error('Error fetching companies', error);
  } finally {
    state.isLoading = false;
  }
})
</script>
