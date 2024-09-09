<script setup>
const props = defineProps({
  modelValue: Array
});

const emit = defineEmits(['update:modelValue']);

const addExperience = () => {
  emit('update:modelValue', [...props.modelValue, { position: '', company: '', startDate: '', endDate: '' }]);
};

const removeExperience = (index) => {
  const updated = [...props.modelValue];
  updated.splice(index, 1);
  emit('update:modelValue', updated);
};
</script>

<template>
  <div class="mb-4">
    <h3 class="text-lg font-bold">Досвід роботи</h3>
    <div v-for="(experience, index) in modelValue" :key="index">
      <InputField v-model="experience.position" label="Посада" />
      <InputField v-model="experience.company" label="Компанія" />
      <DatePicker v-model="experience.startDate" label="Дата початку" />
      <DatePicker v-model="experience.endDate" label="Дата завершення" />
      <button @click="removeExperience(index)" type="button">Видалити досвід</button>
    </div>
    <button @click="addExperience" type="button">Додати досвід</button>
  </div>
</template>

<style scoped>

</style>