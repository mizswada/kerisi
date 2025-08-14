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

// Chat state
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

const sendMessage = () => {
  if (newMessage.value.trim()) {
    const userMessage = {
      id: Date.now(),
      type: 'user',
      content: newMessage.value.trim(),
      timestamp: new Date(),
    };
    
    messages.value.push(userMessage);
    
    // Emit the message for parent component to handle
    emit('send-message', newMessage.value.trim());
    
    // Clear input
    newMessage.value = '';
    
    // Scroll to bottom
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
          <div class="bg-[#f1c890] text-white px-4 py-3 rounded-t-lg flex items-center justify-between">
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
              class="text-white hover:text-gray-200 transition-colors"
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
                       ? 'bg-[#f1c890] text-white' 
                       : 'bg-gray-200 text-gray-800'
                   ]"
                 >
                  <p class="text-sm">{{ message.content }}</p>
                                     <p 
                     :class="[
                       'text-xs mt-1',
                       message.type === 'user' ? 'text-[#f1c890]/80' : 'text-gray-500'
                     ]"
                   >
                    {{ new Date(message.timestamp).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' }) }}
                  </p>
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
                 class="flex-1 px-3 py-2 border border-gray-300 rounded-lg focus:outline-none focus:ring-2 focus:ring-[#f1c890] focus:border-transparent"
               />
               <button
                 @click="sendMessage"
                 :disabled="!newMessage.trim()"
                 class="px-4 py-2 bg-[#f1c890] text-white rounded-lg hover:bg-[#e6b880] disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
               >
                Send
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
