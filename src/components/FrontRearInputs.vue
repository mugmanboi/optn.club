<script setup lang="ts">
import { v1 as uuid } from 'uuid';
import {
  computed,
  reactive,
  watch,
} from 'vue';

import { FrontAndRearSettings } from '../lib/types';

const props = withDefaults(defineProps<{
  modelValue: FrontAndRearSettings;
  label?: string;
  placeholder?: string;
  required?: boolean;
  step?: number | string;
  min?: number | string;
  max?: number | string;
  attachRight?: boolean;
  attachLeft?: boolean;
  disabled?: boolean;
}>(), {
  label: '',
  placeholder: 'Stock',
  required: false,
  step: 1,
  min: 0,
  max: 9999,
});

const emit = defineEmits<{
  (e: 'update:modelValue', v: FrontAndRearSettings): void,
}>();

// eslint-disable-next-line vue/no-setup-props-reactivity-loss
const state = reactive({ form: { ...props.modelValue } });

const id = uuid();

watch(state, () => {
  emit('update:modelValue', state.form);
}, { deep: true });

watch([() => props.modelValue.front, () => props.modelValue.rear], () => {
  if (props.modelValue.front !== state.form.front || props.modelValue.rear !== state.form.rear) {
    state.form = { ...props.modelValue };
  }
});
</script>

<template>
  <div
    class="control !min-w-[250px]"
    :class="{ disabled }"
  >
    <div class="label">{{ label }}</div>
    <div class="flex w-full">
      <slot name="attach-left" />
      <div class="relative flex-1 sm:flex-auto">
        <label
          :for="`${id}front`"
          class="prefix"
        >F</label>
        <input
          :id="`${id}front`"
          v-model="state.form.front"
          :placeholder="placeholder"
          class="rounded-r-none"
          :class="{ 'rounded-l-none': attachLeft }"
          type="number"
          :step="step"
          :min="min"
          :max="max"
          :disabled="disabled"
        >
      </div>
      <div class="relative flex-1 sm:flex-auto">
        <label
          :for="`${id}rear`"
          class="prefix"
        >R</label>
        <input
          :id="`${id}rear`"
          v-model="state.form.rear"
          :placeholder="placeholder"
          class="rounded-l-none"
          :class="{ 'rounded-r-none': attachRight }"
          type="number"
          :step="step"
          :min="min"
          :max="max"
          :disabled="disabled"
        >
      </div>
      <slot name="attach-right" />
    </div>
  </div>
</template>
