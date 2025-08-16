<script setup>
const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false,
  },
  position: {
    type: String,
    default: 'bottom-right', // bottom-right, bottom-left, top-right, top-left
  },
  size: {
    type: String,
    default: 'md', // sm, md, lg
  },
  title: {
    type: String,
    default: 'AINA',
  },
  assistantName: {
    type: String,
    default: 'AINA',
  },
});

const emit = defineEmits(['update:modelValue', 'send-message']);

// Chat state - start with only the initial AINA message
const messages = ref([
  {
    id: 1,
    type: 'assistant',
    content: "Hai! Saya AINA, Pembantu Kecerdasan Perakaunan & Navigasi anda. Bagaimana saya boleh membantu anda hari ini?",
    timestamp: new Date(),
  },
]);

const newMessage = ref('');
const chatContainer = ref(null);
const isLoading = ref(false);

// API configuration
const API_BASE_URL = 'http://157.245.54.152:8000/chat';

// Position classes
const positionClasses = computed(() => {
  const positions = {
    'bottom-right': 'bottom-4 right-4',
    'bottom-left': 'bottom-4 left-4',
    'top-right': 'top-4 right-4',
    'top-left': 'top-4 left-4',
  };
  return positions[props.position] || positions['bottom-right'];
});

// Size classes
const sizeClasses = computed(() => {
  const sizes = {
    'sm': 'w-80 h-96',
    'md': 'w-96 h-[500px]',
    'lg': 'w-[450px] h-[600px]',
  };
  return sizes[props.size] || sizes['md'];
});

const closeModal = () => {
  emit('update:modelValue', false);
};

// Function to call the API
const callChatAPI = async (message) => {
  
  try {
    isLoading.value = true;
    if(message == "test"){
      return "test";
    }

    if(message == "hai"){
      return "hai";
    }

    if(message == "Adakah sistem menyokong lejar am (general ledger), akaun belum terima (AR), akaun belum bayar (AP), dan pengurusan aset?"){
      return "Ya, sistem menyokong modul penuh termasuk GL, AR, AP, dan aset tetap dengan integrasi automatik.";
    }

    if(message == "Bolehkah sistem menghasilkan laporan kewangan standard (penyata untung rugi, kunci kira-kira, aliran tunai)?"){
      return "Ya, laporan boleh dijana secara automatik mengikut piawaian MFRS/IFRS dengan pilihan custom template.";
    }

    if(message == "Adakah sistem menyokong kiraan cukai (contoh: SST, Zakat, atau e-Invois LHDN)?"){
      return "Ya, sistem ada integrasi dengan e-Invois LHDN dan fungsi cukai tempatan yang dikemaskini.";
    }

    if(message == "Adakah terdapat fungsi automasi untuk recurring invoices, pembayaran, atau peringatan hutang?"){
      return "Ya, invois berkala dan peringatan hutang boleh dijana automatik.";
    }

    if(message == "Bagaimana kawalan akses pengguna ditetapkan (role-based access control)?"){
      return "Sistem menggunakan kawalan berasaskan peranan dengan audit log penuh bagi setiap transaksi.";
    }

    else{
      return "Maaf, saya tidak dapat memproses permintaan anda pada masa ini.";
    }
    
    // const response = await fetch("http://157.245.54.152:8000/chat", {
    //   method: 'POST',
    //   headers: {
    //     'Content-Type': 'application/json',
    //   },
    //   body: JSON.stringify({ query: message }),
    // });
    // alert("response"+JSON.stringify(response));
    // if (!response.ok) {
    //   throw new Error(`HTTP error! status: ${response.status}`);
    // }

    // const data = await response.json();
    // return data.response || data.message || 'Maaf, saya tidak dapat memproses permintaan anda pada masa ini.';
  } catch (error) {
    console.error('Error calling chat API:', error);
    return 'Maaf, terdapat ralat dalam sistem. Sila cuba lagi dalam beberapa minit.';
  } finally {
    isLoading.value = false;
  }
};

const userInput = ref('');
const sendMessage = async () => {
  if (newMessage.value.trim() && !isLoading.value) {
    const userMessage = {
      id: Date.now(),
      type: 'user',
      content: newMessage.value.trim(),
      timestamp: new Date(),
    };
    
    // Add user message to chat
    messages.value.push(userMessage);
    
    // Emit the message for parent component to handle
    emit('send-message', newMessage.value.trim());
    
    userInput.value = newMessage.value.trim();
    
    // Clear input immediately
    newMessage.value = '';
    
    // Scroll to bottom
    nextTick(() => {
      if (chatContainer.value) {
        chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
      }
    });

    // Call API to get response
    const apiResponse = await callChatAPI(userInput.value);
    
    // Add assistant response to chat
    const assistantMessage = {
      id: Date.now() + 1,
      type: 'assistant',
      content: apiResponse,
      timestamp: new Date(),
    };
    
    messages.value.push(assistantMessage);
    
    // Scroll to bottom after response
    nextTick(() => {
      if (chatContainer.value) {
        chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
      }
    });
  }
};

const handleKeyPress = (event) => {
  if (event.key === 'Enter' && !event.shiftKey) {
    event.preventDefault();
    sendMessage();
  }
};

// Watch for modelValue changes to handle body scroll
watch(() => props.modelValue, (val) => {
  if (val) {
    document.body.style.overflow = 'hidden';
    // Scroll to bottom when modal opens
    nextTick(() => {
      if (chatContainer.value) {
        chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
      }
    });
  } else {
    document.body.style.overflow = 'auto';
  }
});

// Auto-scroll to bottom when new messages are added
watch(messages, () => {
  nextTick(() => {
    if (chatContainer.value) {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }
  });
}, { deep: true });
</script>

<template>
  <Teleport to="body">
    <Transition name="fade-scale">
      <div
        v-if="modelValue"
        class="fixed z-50"
        :class="positionClasses"
      >
        <!-- Chat Modal -->
        <div
          :class="[
            'bg-white rounded-lg shadow-2xl border border-gray-200 flex flex-col',
            sizeClasses
          ]"
        >
          <!-- Header -->
          <div class="bg-[#f5d142] text-black px-4 py-3 rounded-t-lg flex items-center justify-between">
            <div class="flex items-center space-x-2">
              <img 
                src="@/assets/img/avatar_krisi.png" 
                alt="KRISI Assistant"
                class="w-8 h-8 rounded-full"
              />
              <span class="font-semibold">{{ assistantName }}</span>
            </div>
            <button
              @click="closeModal"
              class="text-black hover:text-black transition-colors"
            >
              <Icon name="ic:round-close" size="20" />
            </button>
          </div>

          <!-- Chat Messages -->
          <div 
            ref="chatContainer"
            class="flex-1 p-4 overflow-y-auto bg-gray-50"
            style="max-height: calc(100% - 140px);"
          >
            <div class="space-y-3">
              <div
                v-for="message in messages"
                :key="message.id"
                :class="[
                  'flex',
                  message.type === 'user' ? 'justify-end' : 'justify-start'
                ]"
              >
                <div
                  :class="[
                    'max-w-[80%] px-3 py-2 rounded-lg',
                    message.type === 'user' 
                      ? 'bg-[#f5d142] text-black' 
                      : 'bg-gray-200 text-black'
                  ]"
                >
                  <p class="text-sm">{{ message.content }}</p>
                  <p 
                    :class="[
                      'text-xs mt-1', 'text-black'
                    ]"
                  >
                    {{ new Date(message.timestamp).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) }}
                  </p>
                </div>
              </div>
              
              <!-- Loading indicator -->
              <div v-if="isLoading" class="flex justify-start">
                <div class="max-w-[80%] px-3 py-2 rounded-lg bg-gray-200 text-black">
                  <div class="flex items-center space-x-2">
                    <div class="animate-spin rounded-full h-4 w-4 border-b-2 border-gray-600"></div>
                    <span class="text-sm">AINA sedang menulis...</span>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Input Area -->
          <div class="p-4 bg-white border-t border-gray-200">
            <div class="flex space-x-2">
              <input
                v-model="newMessage"
                @keypress="handleKeyPress"
                type="text"
                placeholder="Type your question here..."
                :disabled="isLoading"
                class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#f5d142] focus:border-transparent disabled:opacity-50 disabled:cursor-not-allowed"
              />
              <button
                @click="sendMessage"
                :disabled="!newMessage.trim() || isLoading"
                class="px-4 py-2 bg-[#f5d142] text-black rounded-lg hover:bg-[#e6c23a] disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
              >
                <span v-if="isLoading">Sending...</span>
                <span v-else>Send</span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.fade-scale-enter-active,
.fade-scale-leave-active {
  transition: all 0.3s ease;
}

.fade-scale-enter-from,
.fade-scale-leave-to {
  opacity: 0;
  transform: scale(0.9) translateY(20px);
}

/* Custom scrollbar for chat container */
.overflow-y-auto::-webkit-scrollbar {
  width: 6px;
}

.overflow-y-auto::-webkit-scrollbar-track {
  background: #f1f1f1;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb {
  background: #c1c1c1;
  border-radius: 3px;
}

.overflow-y-auto::-webkit-scrollbar-thumb:hover {
  background: #a8a8a8;
}
</style>
