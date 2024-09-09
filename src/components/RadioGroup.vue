<script setup>
const props = defineProps({
  modelValue: String,
  label: String,
  options: Array,
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
    <label class="block mb-2 text-gray-700">{{ label }}</label>
    <div v-for="option in options" :key="option">
      <label :for="`${id}-${option}`">
        <input type="radio" :id="`${id}-${option}`" :value="option" :checked="modelValue === option" @change="updateValue(option)" />
        {{ option }}
      </label>
    </div>
    <p v-if="error" class="text-red-500 mt-1">{{ error }}</p>
  </div>
</template>

<style scoped>

</style>