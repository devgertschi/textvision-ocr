<template>
  <div class="min-h-screen bg-gradient-to-b from-gray-50 to-gray-100 flex flex-col">
    <AppHeader />
    <main class="flex-1 container mx-auto px-4 py-8 max-w-4xl">
      <OCRScanner />
    </main>
    <footer class="py-6 text-center text-gray-500 text-sm">
      <p>&copy; {{ new Date().getFullYear() }} TextVision OCR. All rights reserved.</p>
    </footer>
  </div>
</template>