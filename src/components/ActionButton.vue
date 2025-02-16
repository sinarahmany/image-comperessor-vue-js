<template>
    <button
      :disabled="disabled"
      :class="computedClass"
      @click="handleClick"
    >
      <!-- Optional icon slot -->
      <slot name="icon" />
      <span v-if="label">{{ label }}</span>
    </button>
  </template>
  
  <script setup>
  import { computed, defineProps } from 'vue';
  
  const props = defineProps({
    label: {
      type: String,
      default: ""
    },
    disabled: {
      type: Boolean,
      default: false
    },
    onClick: {
      type: Function,
      default: null
    },
    // variant can be 'primary', 'success', or 'disabled'
    variant: {
      type: String,
      default: 'primary'
    }
  });
  
  const handleClick = (event) => {
    if (!props.disabled && props.onClick) {
      props.onClick(event);
    }
  };
  
  const baseClasses =
    "px-4 py-2 rounded-md mr-2 mb-2 text-sm leading-6 font-semibold transition-colors duration-150 ease-in-out inline-flex items-center";
  
  const computedClass = computed(() => {
    let variantClasses = "";
    switch (props.variant) {
      case "success":
        variantClasses = "bg-green-600 text-white hover:bg-green-700 cursor-pointer";
        break;
      case "disabled":
        // Note: When disabled, the cursor should show that the button is inactive.
        variantClasses = "bg-indigo-500 text-white cursor-not-allowed";
        break;
      default:
        variantClasses = "bg-indigo-500 text-white hover:bg-indigo-700 cursor-pointer";
    }
    return `${baseClasses} ${variantClasses}`;
  });
  </script>
  