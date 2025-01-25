<template>
  <section class="bg-green-50">
    <div class="container m-auto max-w-2xl py-24">
      <div
        class="bg-white px-6 py-8 mb-4 shadow-md rounded-md border m-4 md:m-0"
      >
        <form @submit.prevent="handleSubmit">
          <h2 class="text-3xl text-center font-semibold mb-6">新公司入驻</h2>

          <div class="mb-4">
            <label for="company" class="block text-gray-700 font-bold mb-2"
              >公司名称</label
            >
            <input
              v-model="form.name"
              type="text"
              id="company"
              name="company"
              class="border rounded w-full py-2 px-3"
            />
          </div>

          <div class="mb-4">
            <label
              for="company_description"
              class="block text-gray-700 font-bold mb-2"
              >公司描述</label
            >
            <textarea
              v-model="form.description"
              id="company_description"
              name="company_description"
              class="border rounded w-full py-2 px-3"
              rows="4"
            ></textarea>
          </div>

          <div class="mb-4">
            <label
              for="contact_email"
              class="block text-gray-700 font-bold mb-2"
              >邮箱</label
            >
            <input
              v-model="form.contactEmail"
              type="email"
              id="contact_email"
              name="contact_email"
              class="border rounded w-full py-2 px-3"
              required
            />
          </div>
          <div class="mb-4">
            <label
              for="contact_phone"
              class="block text-gray-700 font-bold mb-2"
              >联系电话</label
            >
            <input
              v-model="form.contactPhone"
              type="tel"
              id="contact_phone"
              name="contact_phone"
              class="border rounded w-full py-2 px-3"
            />
          </div>
          <div>
            <button
              class="bg-green-500 hover:bg-green-600 text-white font-bold py-2 px-4 rounded-full w-full focus:outline-none focus:shadow-outline"
              type="submit"
            >
              登记
            </button>
          </div>
        </form>
      </div>
    </div>
  </section>
</template>
<script setup>
import supabase from '@/config/supabaseClient';
import { reactive } from 'vue';
import { useToast } from 'vue-toastification';
const toast = useToast();
const form = reactive({
  name: '',
  description: '',
  contactEmail: '',
  contactPhone: '',
});
const handleSubmit = async () => {
  console.log('1');
  const newCompany = {
    name: form.name,
    description: form.description,
    contactEmail: form.contactEmail,
    contactPhone: form.contactPhone,
  };
  try {
    const { error } = await supabase.from('company').insert([newCompany]);

    if (error) {
      throw new Error(error.message);
    }
    toast.success('入驻登记成功');
  } catch (error) {
    console.error(error);
    toast.error('入驻登记失败');
  }
};
</script>
