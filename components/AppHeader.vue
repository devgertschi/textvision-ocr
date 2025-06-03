<template>
	<header class="bg-white shadow-sm">
		<div class="container mx-auto px-4 py-4 max-w-4xl flex items-center">
			<div class="flex items-center space-x-2">
				<ScanText class="h-7 w-7 text-blue-600" />
				<h1 class="text-xl font-semibold text-gray-800">
					<a href="https://gertschi.dev">gertschi.dev</a> | TextVision OCR
				</h1>
			</div>
			<div class="ml-auto">
				<a
					href="https://github.com/devgertschi/textvision-ocr"
					target="_blank"
					rel="noopener noreferrer"
					class="text-gray-600 hover:text-gray-900 text-sm font-medium"
				>
					About
				</a>
			</div>
		</div>
	</header>
</template>

<script setup>
import { ScanText } from 'lucide-vue-next';
</script>
