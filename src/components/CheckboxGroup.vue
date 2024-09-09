<script setup>
const props = defineProps({
  modelValue: {
    type: Array,
    default: () => []
  },
  label: String,
  options: Array,
  error: String
});

const emit = defineEmits(['update:modelValue']);

const toggleCheckbox = (option) => {
  const value = [...props.modelValue];
  if (value.includes(option)) {
    const index = value.indexOf(option);
    value.splice(index, 1);
  } else {
    value.push(option);
  }
  emit('update:modelValue', value);
};
</script>

<template>
  <div class="mb-4">
    <label class="block mb-2 text-gray-700">{{ label }}</label>
    <div v-for="option in options" :key="option">
      <label :for="option">
        <input type="checkbox" :id="option" :value="option" :checked="modelValue.includes(option)" @change="toggleCheckbox(option)" />
        {{ option }}
      </label>
    </div>
    <p v-if="error" class="text-red-500 mt-1">{{ error }}</p>
  </div>
</template>

<style scoped>

</style>