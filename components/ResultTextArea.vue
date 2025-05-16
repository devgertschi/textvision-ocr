<template>
  <div class="space-y-2">
    <div class="flex justify-between items-center">
      <h3 class="text-sm font-medium text-gray-700">Extracted text</h3>
      <button
        v-if="modelValue"
        @click="copyToClipboard"
        :disabled="isProcessing"
        :class="[
          'inline-flex items-center px-2.5 py-1.5 text-xs font-medium rounded transition-colors duration-200 ease-in-out',
          copied 
            ? 'bg-green-100 text-green-800' 
            : 'bg-gray-100 text-gray-700 hover:bg-gray-200',
          isProcessing ? 'opacity-50 cursor-not-allowed' : ''
        ]"
        type="button"
      >
        <component :is="copied ? Check : Copy" class="h-3.5 w-3.5 mr-1" />
        {{ copied ? 'Copied!' : 'Copy Text' }}
      </button>
    </div>
    
    <div :class="[
      'relative rounded-lg overflow-hidden transition-all duration-300 border',
      modelValue ? 'border-gray-300' : 'border-gray-200'
    ]">
      <textarea
        :value="modelValue"
        @input="$emit('update:modelValue', $event.target.value)"
        :placeholder="isProcessing ? 'Processing image...' : 'Extracted text will appear here'"
        :class="[
          'w-full h-[300px] p-4 resize-none focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent',
          modelValue ? 'bg-white' : 'bg-gray-50',
          isProcessing ? 'text-gray-400' : 'text-gray-800'
        ]"
        :disabled="isProcessing"
      />
      
      <div v-if="isProcessing" class="absolute inset-0 bg-white bg-opacity-60 flex items-center justify-center">
        <div class="animate-pulse text-gray-500 text-sm">
          Extracting text...
        </div>
      </div>
      
      <div v-if="!modelValue && !isProcessing" class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 text-center w-full px-4">
        <p class="text-gray-400 text-sm">
          Paste or upload an image to extract its text
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { Copy, Check } from 'lucide-vue-next'

const props = defineProps({
  modelValue: String,
  isProcessing: Boolean
})

const emit = defineEmits(['update:modelValue'])
const copied = ref(false)

const copyToClipboard = async () => {
  try {
    await navigator.clipboard.writeText(props.modelValue)
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 2000)
  } catch (err) {
    console.error('Failed to copy text: ', err)
  }
}
</script>