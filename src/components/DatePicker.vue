<script setup>
const props = defineProps({
  modelValue: String,
  label: String,
  error: String,
  id: String
});

const emit = defineEmits(['update:modelValue']);

const updateValue = (value) => {
  emit('update:modelValue', value);
};
</script>

<template>
  <div class="mb-4">
    <label :for="id" class="block mb-2 text-gray-700">{{ label }}</label>
    <input type="date" :id="id" :value="modelValue" @input="updateValue($event.target.value)" class="border p-2 w-full" />
    <p v-if="error" class="text-red-500 mt-1">{{ error }}</p>
  </div>
</template>

<style scoped>

</style>