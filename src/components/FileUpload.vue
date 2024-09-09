<script setup>
const props = defineProps({
  label: String,
  id: String,
  multiple: {
    type: Boolean,
    default: false
  },
  error: String
});

const emit = defineEmits(['update:files']);

const handleFilesUpload = (event) => {
  const files = event.target.files;
  emit('update:files', files);
};
</script>

<template>
  <div class="mb-4">
    <label :for="id" class="block mb-2 text-gray-700">{{ label }}</label>
    <input type="file" :id="id" :multiple="multiple" @change="handleFilesUpload" />
    <p v-if="error" class="text-red-500 mt-1">{{ error }}</p>
  </div>
</template>

<style scoped>

</style>