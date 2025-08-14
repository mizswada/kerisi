<script setup>
const props = defineProps({
  position: {
    type: String,
    default: 'bottom-right', // bottom-right, bottom-left, top-right, top-left
  },
  size: {
    type: String,
    default: 'md', // sm, md, lg
  },
  show: {
    type: Boolean,
    default: true,
  },
  badge: {
    type: [String, Number],
    default: null,
  },
  badgeColor: {
    type: String,
    default: 'danger', // primary, secondary, success, danger, warning, info
  },
  tooltip: {
    type: String,
    default: 'Chat with us',
  },
  onClick: {
    type: Function,
    default: () => {},
  },
  // New props for image support
  image: {
    type: String,
    default: null, // URL of the image to display
  },
  imageAlt: {
    type: String,
    default: 'Chat',
  },
  useImage: {
    type: Boolean,
    default: false, // Set to true to use image instead of icon
  },
  imageSize: {
    type: String,
    default: 'auto', // auto, sm, md, lg, or specific size like '24px'
  },
});

const emit = defineEmits(['click']);

const isVisible = ref(props.show);

const positionClasses = computed(() => {
  const positions = {
    'bottom-right': 'bottom-4 right-4',
    'bottom-left': 'bottom-4 left-4',
    'top-right': 'top-4 right-4',
    'top-left': 'top-4 left-4',
  };
  return positions[props.position] || positions['bottom-right'];
});

const sizeClasses = computed(() => {
  const sizes = {
    'sm': 'w-12 h-12',
    'md': 'w-14 h-14',
    'lg': 'w-16 h-16',
    'xl': 'w-[100px] h-[100px]',
  };
  return sizes[props.size] || sizes['md'];
});

const badgeClasses = computed(() => {
  const colors = {
    'primary': 'bg-primary text-white',
    'secondary': 'bg-secondary text-white',
    'success': 'bg-success text-white',
    'danger': 'bg-danger text-white',
    'warning': 'bg-warning text-white',
    'info': 'bg-info text-white',
  };
  return colors[props.badgeColor] || colors['danger'];
});

const imageSizeClasses = computed(() => {
  if (props.imageSize === 'auto') {
    const sizes = {
      'sm': 'w-8 h-8',
      'md': 'w-10 h-10',
      'lg': 'w-12 h-12',
    };
    return sizes[props.size] || sizes['md'];
  }
  return props.imageSize;
});

const handleClick = () => {
  emit('click');
  if (props.onClick) {
    props.onClick();
  }
};

watch(() => props.show, (newValue) => {
  isVisible.value = newValue;
});
</script>

<template>
  <Transition name="fade-scale">
    <div
      v-if="isVisible"
      :class="[
        'fixed z-[60] cursor-pointer transition-all duration-300 hover:scale-110',
        positionClasses,
        sizeClasses
      ]"
      @click="handleClick"
      :title="tooltip"
    >
      <!-- Floating Chat Button -->
      <div class="relative w-full h-full">
        <!-- Main Button -->
                 <div class="w-full h-full bg-[#f1c890] text-white rounded-full shadow-lg hover:shadow-xl flex items-center justify-center transition-all duration-300 overflow-hidden">
          <!-- Image Mode -->
          <img
            v-if="useImage && image"
            src="@/assets/img/avatar_krisi.png"
            :alt="imageAlt"
            class="w-full h-full object-cover rounded-full"
          />
          <!-- Icon Mode (fallback) -->
          <Icon 
            v-else
            name="ic:baseline-chat" 
            :size="props.size === 'sm' ? '20' : props.size === 'lg' ? '28' : '24'"
            class="text-white"
          />
        </div>
        
        <!-- Badge -->
        <div
          v-if="badge"
          :class="[
            'absolute -top-1 -right-1 min-w-[20px] h-5 px-1 rounded-full text-xs font-bold flex items-center justify-center',
            badgeClasses
          ]"
        >
          {{ badge }}
        </div>
        
        <!-- Pulse Animation -->
        <div class="absolute inset-0 bg-[#f1c890] rounded-full animate-ping opacity-20"></div>
      </div>
    </div>
  </Transition>
</template>

<style scoped>
.fade-scale-enter-active,
.fade-scale-leave-active {
  transition: all 0.3s ease;
}

.fade-scale-enter-from,
.fade-scale-leave-to {
  opacity: 0;
  transform: scale(0.8);
}

@keyframes ping {
  75%, 100% {
    transform: scale(1.2);
    opacity: 0;
  }
}

.animate-ping {
  animation: ping 2s cubic-bezier(0, 0, 0.2, 1) infinite;
}
</style>
