<template>
  <div class="space-y-4">
    <h3 class="text-sm font-medium text-gray-700">Image input</h3>
    <div 
      ref="dropzoneRef"
      :class="[
        'relative border-2 border-dashed rounded-lg p-6 transition-all',
        isDragging ? 'border-blue-500 bg-blue-50' : 'border-gray-300 hover:border-gray-400',
        imageUrl ? 'border-blue-200 bg-blue-50' : '',
        isProcessing ? 'opacity-50 pointer-events-none' : 'cursor-pointer'
      ]"
      @click="handleClick"
      @dragover.prevent="handleDragOver"
      @dragleave="handleDragLeave"
      @drop.prevent="handleDrop"
    >
      <input 
        ref="fileInputRef"
        type="file"
        accept="image/*"
        class="hidden"
        @change="handleFileSelect"
        :disabled="isProcessing"
      />

      <div v-if="imageUrl" class="relative">
        <img 
          :src="imageUrl" 
          alt="Uploaded" 
          class="w-full h-auto rounded object-contain max-h-[300px]"
        />
        <button 
          v-if="!isProcessing"
          @click.stop="$emit('reset')"
          class="absolute top-2 right-2 bg-gray-800 bg-opacity-70 hover:bg-opacity-90 text-white rounded-full p-1 transition-colors"
          aria-label="Remove image"
        >
          <X class="h-4 w-4" />
        </button>
      </div>
      <div v-else class="text-center py-12">
        <div class="flex justify-center">
          <div class="bg-gray-100 rounded-full p-3 mb-4">
            <Upload class="h-6 w-6 text-gray-500" />
          </div>
        </div>
        <p class="text-sm font-medium text-gray-700">
          Paste an image, drop it here, or click to upload
        </p>
        <p class="text-xs text-gray-500 mt-1">
          Supports PNG, JPG, GIF
        </p>
      </div>
    </div>

    <div v-if="imageUrl && !isProcessing" class="flex justify-between">
      <button
        @click="$emit('reset')"
        class="text-sm text-gray-600 hover:text-gray-900 flex items-center"
        type="button"
      >
        <X class="h-4 w-4 mr-1" />
        Clear Image
      </button>
      <button
        @click="triggerFileInput"
        class="text-sm text-blue-600 hover:text-blue-800 flex items-center"
        type="button"
      >
        <ImageIcon class="h-4 w-4 mr-1" />
        Try Different Image
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { Upload, X, Image as ImageIcon } from 'lucide-vue-next'

const props = defineProps({
  imageUrl: String,
  isProcessing: Boolean
})

const emit = defineEmits(['imagePaste', 'reset'])

const dropzoneRef = ref(null)
const fileInputRef = ref(null)
const isDragging = ref(false)

const handlePaste = (e) => {
  e.preventDefault()
  const items = e.clipboardData?.items
  
  if (!items) return
  
  for (let i = 0; i < items.length; i++) {
    if (items[i].type.indexOf('image') !== -1) {
      const blob = items[i].getAsFile()
      if (blob) {
        const url = URL.createObjectURL(blob)
        emit('imagePaste', url)
        return
      }
    }
  }
}

const handleFileSelect = (e) => {
  const files = e.target.files
  if (files && files.length > 0) {
    const url = URL.createObjectURL(files[0])
    emit('imagePaste', url)
  }
}

const handleDragOver = (e) => {
  e.preventDefault()
  isDragging.value = true
}

const handleDragLeave = () => {
  isDragging.value = false
}

const handleDrop = (e) => {
  isDragging.value = false
  
  const files = e.dataTransfer.files
  if (files && files.length > 0) {
    const file = files[0]
    if (file.type.startsWith('image/')) {
      const url = URL.createObjectURL(file)
      emit('imagePaste', url)
    }
  }
}

const handleClick = () => {
	if (!props.imageUrl) {
		triggerFileInput();
	}
}

const triggerFileInput = () => {
  fileInputRef.value?.click()
}

onMounted(() => {
  document.addEventListener('paste', handlePaste)
})

onUnmounted(() => {
  document.removeEventListener('paste', handlePaste)
})
</script>