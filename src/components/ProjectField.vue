<script setup>
const props = defineProps({
  modelValue: Array
});

const emit = defineEmits(['update:modelValue']);

const addProject = () => {
  emit('update:modelValue', [...props.modelValue, { name: '', description: '', role: '', link: '' }]);
};

const removeProject = (index) => {
  const updated = [...props.modelValue];
  updated.splice(index, 1);
  emit('update:modelValue', updated);
};
</script>

<template>
  <div class="mb-4">
    <h3 class="text-lg font-bold">Проекти</h3>
    <div v-for="(project, index) in modelValue" :key="index">
      <InputField v-model="project.name" label="Назва проекту" />
      <InputField v-model="project.description" label="Опис" />
      <InputField v-model="project.role" label="Роль" />
      <InputField v-model="project.link" label="Посилання" />
      <button @click="removeProject(index)" type="button">Видалити проект</button>
    </div>
    <button @click="addProject" type="button">Додати проект</button>
  </div>
</template>

<style scoped>

</style>