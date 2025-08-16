<script setup>
import { ref, computed, watch, nextTick } from 'vue';
import { marked } from 'marked';

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
    content: "Hi! I'm AINA, your Accounting & Navigation Intelligence Assistant. How can I help you today?",
    timestamp: new Date(),
  },
]);

const newMessage = ref('');
const chatContainer = ref(null);
const isLoading = ref(false);
const showMetadataModal = ref(false);
const metadataDetails = ref(null);

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

// Function to linkify IDs in HTML content
const linkifyIDs = (html) => {
  // Regex patterns for each type (adjust as needed)
  const patterns = [
    { type: 'po', regex: /(PO\d{6,}|PRO\d{3}\/\d{4})/g },
    { type: 'bill', regex: /(APN\d{6}\/\d{4}|IR\d{9,})/g },
    { type: 'voucher', regex: /(VCR\d{6,}\/\d{4})/g },
    { type: 'payment', regex: /(EFT\d{5,}\/\d{4})/g }
  ];
  
  patterns.forEach(({type, regex}) => {
    html = html.replace(regex, match =>
      `<span class="id-link cursor-pointer text-blue-600 hover:text-blue-800 underline" data-type="${type}" data-id="${match}">${match}</span>`
    );
  });
  return html;
};

// Function to show metadata modal
const showMetadataModalData = (data) => {
  if (!data || Object.keys(data).length === 0) {
    metadataDetails.value = '<p>No details found.</p>';
  } else {
    metadataDetails.value = `
      <h3 class="text-lg font-semibold mb-3">Details</h3>
      <table class="w-full border-collapse">
        ${Object.entries(data).map(([k, v]) =>
          `<tr><td class="font-bold p-2 border-b border-gray-200">${k}</td><td class="p-2 border-b border-gray-200">${v}</td></tr>`
        ).join('')}
      </table>
    `;
  }
  showMetadataModal.value = true;
};

// Function to fetch and show metadata
const fetchAndShowMetadata = async (type, id) => {
  try {
    const response = await fetch(`http://157.245.54.152:8000/api/details?type=${typeMap(type)}&id=${encodeURIComponent(id)}`);
    const data = await response.json();
    showMetadataModalData(data);
  } catch (error) {
    alert('Details not found!');
  }
};

// Function to map types
const typeMap = (type) => {
  if (type === 'po') return 'purchase_order';
  if (type === 'bill') return 'bill';
  if (type === 'voucher') return 'voucher';
  if (type === 'payment') return 'payment';
  return '';
};

// Configure marked for better rendering
marked.setOptions({
  breaks: true,  // Respect line breaks
  gfm: true,     // Use GitHub Flavored Markdown
  sanitize: false // Allow HTML tags for ID linking
});

// Function to parse markdown and linkify IDs
const parseMarkdownAndLinkify = (content) => {
  // First parse markdown to HTML
  let html = marked.parse(content);
  
  // Then apply ID linking
  html = linkifyIDs(html);
  
  return html;
};

// Function to call the API
const callChatAPI = async (message) => {
  try {
    isLoading.value = true;

    if(message.toLowerCase() === "test"){
      return "Hi, I'm AINA, your Accounting & Navigation Intelligence Assistant. How can I help you today?";
    }

    if(message.toLowerCase() === "hai" || message.toLowerCase() === "hi"){
      return "Hi, I'm AINA, your Accounting & Navigation Intelligence Assistant. How can I help you today?";
    }

    if(message === "Does the system support general ledger, accounts receivable (AR), accounts payable (AP), and asset management?"){
      return "Yes, the system supports full modules including GL, AR, AP, and fixed asset management with automatic integration.";
    }

    if(message === "Can the system generate financial reports (profit and loss statement, balance sheet, cash flow)?"){
      return "Yes, financial reports can be generated automatically according to MFRS/IFRS standards with customizable templates.";
    }

    if(message === "Does the system support tax accounting (e.g., SST, Zakat, or e-Invois LHDN)?"){
      return "Yes, the system has integration with e-Invois LHDN and local tax functions.";
    }

    if(message === "Does the system have automation for recurring invoices, payments, or debt reminders?"){
      return "Yes, recurring invoices and debt reminders can be generated automatically.";
    }

    if(message === "How is user access control set up (role-based access control)?"){
      return "The system uses role-based access control with full audit logs for every transaction.";
    }

    // Call the actual API for other queries
    const response = await fetch(API_BASE_URL, {
      method: 'POST',
      headers: {
        'accept': 'application/json',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        query: message,
        top_k: 10,
      }),
    });

    const data = await response.json();
    return data.answer || data.response || data.message || 'Maaf, saya tidak dapat memproses permintaan anda pada masa ini.';
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
      scrollToBottom();
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
      scrollToBottom();
    });
  }
};

const handleKeyPress = (event) => {
  if (event.key === 'Enter' && !event.shiftKey) {
    event.preventDefault();
    sendMessage();
  }
};

// Enhanced scroll to bottom function
const scrollToBottom = () => {
  if (chatContainer.value) {
    // Multiple attempts to scroll with increasing delays
    setTimeout(() => {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }, 10);
    
    setTimeout(() => {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }, 100);
    
    setTimeout(() => {
      chatContainer.value.scrollTop = chatContainer.value.scrollHeight;
    }, 300);
  }
};

// Watch for modelValue changes to handle body scroll
watch(() => props.modelValue, (val) => {
  if (val) {
    document.body.style.overflow = 'hidden';
    // Scroll to bottom when modal opens
    nextTick(() => {
      scrollToBottom();
    });
  } else {
    document.body.style.overflow = 'auto';
  }
});

// Auto-scroll to bottom when new messages are added
watch(messages, () => {
  nextTick(() => {
    scrollToBottom();
  });
}, { deep: true });

// Handle ID link clicks after DOM updates
const handleIdLinkClicks = () => {
  nextTick(() => {
    const idLinks = document.querySelectorAll('.id-link');
    idLinks.forEach(el => {
      el.addEventListener('click', function() {
        const id = this.getAttribute('data-id');
        const type = this.getAttribute('data-type');
        fetchAndShowMetadata(type, id);
      });
    });
  });
};

// Watch for new messages to add click handlers
watch(messages, () => {
  handleIdLinkClicks();
}, { deep: true });

const closeMetadataModal = () => {
  showMetadataModal.value = false;
};
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
                  <div 
                    v-if="message.type === 'assistant'"
                    class="text-sm bot-message"
                    v-html="parseMarkdownAndLinkify(message.content)"
                  ></div>
                  <p 
                    v-else
                    class="text-sm"
                  >{{ message.content }}</p>
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
                    <span class="text-sm">AINA is typing...</span>
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

    <!-- Metadata Modal -->
    <div 
      v-if="showMetadataModal"
      class="fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center"
      @click="closeMetadataModal"
    >
      <div 
        class="bg-white rounded-lg p-6 max-w-2xl w-full mx-4 max-h-[80vh] overflow-y-auto"
        @click.stop
      >
        <div class="flex justify-between items-center mb-4">
          <h2 class="text-xl font-bold">Document Details</h2>
          <button 
            @click="closeMetadataModal"
            class="text-gray-500 hover:text-gray-700"
          >
            <Icon name="ic:round-close" size="24" />
          </button>
        </div>
        <div id="metadata-details" v-html="metadataDetails"></div>
      </div>
    </div>
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

/* Markdown content styling */
.bot-message h1,
.bot-message h2,
.bot-message h3,
.bot-message h4,
.bot-message h5,
.bot-message h6 {
  font-weight: 600;
  margin: 0.75em 0 0.5em 0;
  line-height: 1.2;
  color: #1f2937;
}

.bot-message h1 { 
  font-size: 1.5em; 
  border-bottom: 2px solid #e5e7eb;
  padding-bottom: 0.25em;
}
.bot-message h2 { 
  font-size: 1.3em; 
  border-bottom: 1px solid #f3f4f6;
  padding-bottom: 0.2em;
}
.bot-message h3 { font-size: 1.1em; }

.bot-message p {
  margin: 0.75em 0;
  line-height: 1.6;
  color: #374151;
}

.bot-message ul,
.bot-message ol {
  margin: 0.75em 0;
  padding-left: 1.5em;
}

.bot-message li {
  margin: 0.4em 0;
  line-height: 1.5;
}

.bot-message code {
  background-color: #f3f4f6;
  padding: 0.2em 0.4em;
  border-radius: 0.375em;
  font-family: 'Courier New', monospace;
  font-size: 0.875em;
  color: #dc2626;
  border: 1px solid #e5e7eb;
}

.bot-message pre {
  background-color: #1f2937;
  color: #f9fafb;
  padding: 1em;
  border-radius: 0.5em;
  overflow-x: auto;
  margin: 0.75em 0;
  border: 1px solid #374151;
}

.bot-message pre code {
  background: none;
  padding: 0;
  color: inherit;
  border: none;
}

.bot-message blockquote {
  border-left: 4px solid #3b82f6;
  padding: 0.75em 1em;
  margin: 0.75em 0;
  background-color: #f8fafc;
  border-radius: 0 0.375em 0.375em 0;
  color: #475569;
}

.bot-message strong {
  font-weight: 700;
  color: #1f2937;
}

.bot-message em {
  font-style: italic;
  color: #6b7280;
}

.bot-message a {
  color: #3b82f6;
  text-decoration: underline;
  text-underline-offset: 2px;
}

.bot-message a:hover {
  color: #2563eb;
  text-decoration-thickness: 2px;
}

.bot-message table {
  border-collapse: collapse;
  width: 100%;
  margin: 0.75em 0;
  border-radius: 0.5em;
  overflow: hidden;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
}

.bot-message th,
.bot-message td {
  border: 1px solid #e5e7eb;
  padding: 0.75em;
  text-align: left;
}

.bot-message th {
  background-color: #f9fafb;
  font-weight: 600;
  color: #374151;
}

.bot-message tr:nth-child(even) {
  background-color: #fafafa;
}

.bot-message tr:hover {
  background-color: #f3f4f6;
}

.bot-message hr {
  border: none;
  border-top: 2px solid #e5e7eb;
  margin: 1.5em 0;
}

/* ID link styling within markdown */
.bot-message .id-link {
  cursor: pointer;
  color: #3b82f6 !important;
  text-decoration: underline !important;
  font-weight: 600;
  padding: 0.125em 0.25em;
  border-radius: 0.25em;
  background-color: #eff6ff;
  transition: all 0.2s ease;
}

.bot-message .id-link:hover {
  color: #1d4ed8 !important;
  background-color: #dbeafe;
  text-decoration: none !important;
}
</style>
