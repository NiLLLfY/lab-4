<template>
  <div class="custom-select">
    <label class="select-label">
      <slot name="label"></slot>
    </label>

    <select :value="modelValue" @change="onChange" :disabled="disabled">
      <option value="" disabled>--- Выберите из списка ---</option>
      <option v-for="option in options" :key="option.value" :value="option.value">
        {{ option.label }}
      </option>
    </select>
  </div>
</template>

<script setup lang="ts">
export interface SelectOption {
  value: number | string
  label: string
}

defineProps<{
  modelValue: number | string
  options: SelectOption[]
  disabled?: boolean
}>()

const emit = defineEmits<{
  (e: 'update:modelValue', value: number | string): void
}>()

const onChange = (event: Event) => {
  const target = event.target as HTMLSelectElement
  const value = isNaN(Number(target.value)) ? target.value : Number(target.value)
  emit('update:modelValue', value)
}
</script>

<style scoped>
.custom-select {
  margin-bottom: 1rem;
}
.select-label {
  display: block;
  margin-bottom: 0.5rem;
  color: #333;
}
select {
  padding: 0.5rem;
  width: 100%;
  max-width: 400px;
  border: 1px solid #ccc;
  border-radius: 4px;
}
select:disabled {
  background-color: #f0f0f0;
  cursor: not-allowed;
}
</style>
