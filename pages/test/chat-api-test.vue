<template>
  <div class="min-h-screen bg-gray-50 p-6">
    <div class="max-w-4xl mx-auto">
      <!-- Header -->
      <div class="bg-white rounded-lg shadow-sm p-6 mb-6">
        <h1 class="text-2xl font-bold text-gray-900 mb-2">Chat API Connection Test</h1>
        <p class="text-gray-600">Testing connection to: <code class="bg-gray-100 px-2 py-1 rounded">http://157.245.54.152:8000/chat</code></p>
      </div>

      <!-- Connection Status -->
      <div class="bg-white rounded-lg shadow-sm p-6 mb-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4">Connection Status</h2>
        <div class="flex items-center space-x-3">
          <div 
            :class="[
              'w-4 h-4 rounded-full',
              connectionStatus === 'connected' ? 'bg-green-500' : 
              connectionStatus === 'failed' ? 'bg-red-500' : 'bg-yellow-500'
            ]"
          ></div>
          <span class="font-medium">
            {{ 
              connectionStatus === 'connected' ? 'Connected' : 
              connectionStatus === 'failed' ? 'Connection Failed' : 'Testing...' 
            }}
          </span>
        </div>
        <div v-if="connectionError" class="mt-3 p-3 bg-red-50 border border-red-200 rounded-md">
          <p class="text-red-800 text-sm">{{ connectionError }}</p>
        </div>
      </div>

      <!-- Test Form -->
      <div class="bg-white rounded-lg shadow-sm p-6 mb-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4">Test Chat API</h2>
        
        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-2">Test Message</label>
            <input
              v-model="testMessage"
              type="text"
              placeholder="Enter a test message..."
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            />
          </div>
          
          <div class="flex space-x-3">
            <button
              @click="testConnection"
              :disabled="isTesting"
              class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
            >
              <span v-if="isTesting">Testing...</span>
              <span v-else>Test Connection</span>
            </button>
            
            <button
              @click="testChatAPI"
              :disabled="isTesting || !testMessage.trim()"
              class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
            >
              <span v-if="isTesting">Sending...</span>
              <span v-else>Send Test Message</span>
            </button>
          </div>
        </div>
      </div>

      <!-- Response Display -->
      <div v-if="apiResponse || apiError" class="bg-white rounded-lg shadow-sm p-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4">API Response</h2>
        
        <div v-if="apiResponse" class="mb-4">
          <h3 class="text-md font-medium text-gray-700 mb-2">Success Response:</h3>
          <div class="bg-green-50 border border-green-200 rounded-md p-4">
            <pre class="text-sm text-green-800 whitespace-pre-wrap">{{ JSON.stringify(apiResponse, null, 2) }}</pre>
          </div>
        </div>
        
        <div v-if="apiError" class="mb-4">
          <h3 class="text-md font-medium text-gray-700 mb-2">Error Response:</h3>
          <div class="bg-red-50 border border-red-200 rounded-md p-4">
            <pre class="text-sm text-red-800 whitespace-pre-wrap">{{ apiError }}</pre>
          </div>
        </div>
      </div>

      <!-- Test History -->
      <div v-if="testHistory.length > 0" class="bg-white rounded-lg shadow-sm p-6">
        <h2 class="text-lg font-semibold text-gray-900 mb-4">Test History</h2>
        <div class="space-y-3">
          <div
            v-for="(test, index) in testHistory"
            :key="index"
            class="border border-gray-200 rounded-lg p-4"
          >
            <div class="flex justify-between items-start mb-2">
              <span class="text-sm font-medium text-gray-700">{{ test.timestamp }}</span>
              <span 
                :class="[
                  'px-2 py-1 text-xs rounded-full',
                  test.success ? 'bg-green-100 text-green-800' : 'bg-red-100 text-red-800'
                ]"
              >
                {{ test.success ? 'Success' : 'Failed' }}
              </span>
            </div>
            <p class="text-sm text-gray-600 mb-2"><strong>Message:</strong> {{ test.message }}</p>
            <p class="text-sm text-gray-600"><strong>Response:</strong> {{ test.response }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
definePageMeta({
  title: 'Chat API Test',
  layout: 'default'
});

// Reactive state
const connectionStatus = ref('pending');
const connectionError = ref('');
const testMessage = ref('Hello, this is a test message');
const isTesting = ref(false);
const apiResponse = ref(null);
const apiError = ref(null);
const testHistory = ref([]);

// API configuration
const API_BASE_URL = 'http://157.245.54.152:8000/chat';

// Test basic connection
const testConnection = async () => {
  try {
    isTesting.value = true;
    connectionStatus.value = 'pending';
    connectionError.value = '';
    
    // Test with a simple GET request first (if the endpoint supports it)
    const response = await fetch(API_BASE_URL, {
      method: 'GET',
      headers: {
        'Accept': 'application/json',
      },
    });
    
    if (response.ok) {
      connectionStatus.value = 'connected';
      connectionError.value = '';
    } else {
      connectionStatus.value = 'failed';
      connectionError.value = `HTTP ${response.status}: ${response.statusText}`;
    }
  } catch (error) {
    connectionStatus.value = 'failed';
    connectionError.value = `Connection failed: ${error.message}`;
    console.error('Connection test error:', error);
  } finally {
    isTesting.value = false;
  }
};

// Test chat API with message
const testChatAPI = async () => {
  if (!testMessage.value.trim()) return;
  
  try {
    isTesting.value = true;
    apiResponse.value = null;
    apiError.value = null;
    
    const response = await fetch(API_BASE_URL, {
      method: 'POST',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        query: testMessage.value.trim(),
        top_k: 10,
      }),
    });
    
    if (response.ok) {
      const data = await response.json();
      apiResponse.value = data;
      
      // Add to test history
      testHistory.value.unshift({
        timestamp: new Date().toLocaleString(),
        message: testMessage.value.trim(),
        response: JSON.stringify(data),
        success: true
      });
      
      // Update connection status if it was previously failed
      if (connectionStatus.value === 'failed') {
        connectionStatus.value = 'connected';
        connectionError.value = '';
      }
    } else {
      const errorText = await response.text();
      apiError.value = `HTTP ${response.status}: ${response.statusText}\n${errorText}`;
      
      // Add to test history
      testHistory.value.unshift({
        timestamp: new Date().toLocaleString(),
        message: testMessage.value.trim(),
        response: `Error: ${response.status} - ${response.statusText}`,
        success: false
      });
    }
  } catch (error) {
    apiError.value = `Network error: ${error.message}`;
    console.error('Chat API test error:', error);
    
    // Add to test history
    testHistory.value.unshift({
      timestamp: new Date().toLocaleString(),
      message: testMessage.value.trim(),
      response: `Error: ${error.message}`,
      success: false
    });
  } finally {
    isTesting.value = false;
  }
};

// Auto-test connection on page load
onMounted(() => {
  testConnection();
});
</script>
