<template>
  <div class="min-h-screen bg-gray-50 p-6">
    <div class="max-w-4xl mx-auto">
      <!-- Header -->
      <div class="bg-white rounded-lg shadow-sm p-6 mb-6">
        <h1 class="text-2xl font-bold text-gray-900 mb-2">Test Pages</h1>
        <p class="text-gray-600">Available test pages for development and debugging</p>
      </div>

      <!-- Test Pages Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        <!-- Chat API Test -->
        <div class="bg-white rounded-lg shadow-sm p-6 hover:shadow-md transition-shadow">
          <div class="flex items-center space-x-3 mb-4">
            <div class="w-10 h-10 bg-blue-100 rounded-lg flex items-center justify-center">
              <Icon name="ic:outline-chat" size="20" class="text-blue-600" />
            </div>
            <div>
              <h3 class="text-lg font-semibold text-gray-900">Chat API Test</h3>
              <p class="text-sm text-gray-500">Test chat API connection</p>
            </div>
          </div>
          <p class="text-gray-600 text-sm mb-4">
            Test the connection to the chat API endpoint and verify responses.
          </p>
          <NuxtLink
            to="/test/chat-api-test"
            class="inline-flex items-center px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors"
          >
            Open Test
            <Icon name="ic:outline-arrow-forward" size="16" class="ml-2" />
          </NuxtLink>
        </div>

        <!-- Placeholder for future tests -->
        <div class="bg-white rounded-lg shadow-sm p-6 border-2 border-dashed border-gray-300">
          <div class="flex items-center space-x-3 mb-4">
            <div class="w-10 h-10 bg-gray-100 rounded-lg flex items-center justify-center">
              <Icon name="ic:outline-add" size="20" class="text-gray-500" />
            </div>
            <div>
              <h3 class="text-lg font-semibold text-gray-500">More Tests</h3>
              <p class="text-sm text-gray-400">Coming soon</p>
            </div>
          </div>
          <p class="text-gray-400 text-sm mb-4">
            Additional test pages will be added here as needed.
          </p>
          <button
            disabled
            class="inline-flex items-center px-4 py-2 bg-gray-300 text-gray-500 rounded-lg cursor-not-allowed"
          >
            Coming Soon
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  title: 'Test Pages',
  layout: 'default'
});
</script>