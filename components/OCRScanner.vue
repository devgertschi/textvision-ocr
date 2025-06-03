<template>
	<div class="space-y-8">
		<section class="text-center mb-8">
			<h2 class="text-2xl font-semibold text-gray-800 mb-2">Extract Text from Images</h2>
			<p class="text-gray-600 max-w-2xl mx-auto">
				Paste a screenshot or upload an image to extract text. All processing happens in your
				browser - no data is sent to any server.
			</p>
		</section>

		<div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
			<div>
				<ImageDropZone
					:imageUrl="imageUrl"
					:isProcessing="isProcessing"
					@imagePaste="handleImagePaste"
					@reset="resetState"
				/>

				<div v-if="isProcessing" class="bg-white p-4 rounded-lg shadow-sm">
					<p class="text-sm font-medium text-gray-700 mb-2">Processing Image</p>
					<ProgressBar :progress="progress" />
				</div>

				<div v-if="error" class="bg-red-50 border border-red-200 rounded-lg p-4 flex items-start">
					<AlertCircle class="h-5 w-5 text-red-500 mt-0.5 flex-shrink-0" />
					<p class="ml-3 text-sm text-red-700">{{ error }}</p>
				</div>
			</div>

			<ResultTextArea v-model="extractedText" :isProcessing="isProcessing" />
		</div>
	</div>
</template>

<script setup>
import { ref } from 'vue';
import { createWorker } from 'tesseract.js';
import { AlertCircle } from 'lucide-vue-next';

const imageUrl = ref(null);
const extractedText = ref('');
const isProcessing = ref(false);
const progress = ref(0);
const error = ref(null);
const worker = ref(null);

const processImage = async imageSource => {
	try {
		isProcessing.value = true;
		progress.value = 0;
		error.value = null;

		if (!worker.value) {
			worker.value = await createWorker();
			await worker.value.loadLanguage('eng');
			await worker.value.initialize('eng');
		}

		const {
			data: { text }
		} = await worker.value.recognize(imageSource);

		extractedText.value = text;
		isProcessing.value = false;
		progress.value = 100;
	} catch (err) {
		console.error('OCR Error:', err);
		error.value = 'Failed to extract text. Please try a different image.';
		isProcessing.value = false;
	}
};

const handleImagePaste = url => {
	imageUrl.value = url;
	processImage(url);
};

const resetState = () => {
	imageUrl.value = null;
	extractedText.value = '';
	progress.value = 0;
	error.value = null;
};
</script>
