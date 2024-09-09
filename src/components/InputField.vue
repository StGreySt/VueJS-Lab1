<script setup>
const props = defineProps({
  modelValue: [String, Number],
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
    <input
        :id="id"
        :value="modelValue"
        @input="updateValue($event.target.value)"
        class="border p-2 w-full"
    />
    <p v-if="error" class="text-red-500 mt-1">{{ error }}</p> <!-- Виведення помилки -->
  </div>
</template>

<style scoped>

</style>