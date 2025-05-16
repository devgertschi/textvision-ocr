<template>
  <div class="relative pt-1">
    <div class="overflow-hidden h-2 text-xs flex rounded bg-blue-100">
      <div 
        :style="{ width: `${progress}%` }"
        class="transition-all duration-300 shadow-none flex flex-col text-center whitespace-nowrap text-white justify-center bg-blue-500"
      />
    </div>
    <div class="text-right">
      <span class="text-xs font-semibold inline-block text-blue-600 mt-1">
        {{ Math.round(progress) }}%
      </span>
    </div>
  </div>
</template>

<script setup>
defineProps({
  progress: {
    type: Number,
    required: true
  }
})
</script>