<script setup>
const props = defineProps({
  context: Object,
});

const searchQuery = ref('');
const isOpen = ref(false);
const selectedOptions = ref(props.context._value || []);
const filteredOptions = ref([]);
const focusedIndex = ref(-1);
const inputRef = ref(null);
const dropdownStyle = ref({});

// Get options from context
const options = computed(() => {
  let result;
  
  if (typeof props.context.options === 'function') {
    result = props.context.options({ search: searchQuery.value });
  } else {
    result = props.context.options || [];
  }
  
  // Ensure we always return an array
  if (!Array.isArray(result)) {
    // Handle object format { key: value }
    if (typeof result === 'object' && result !== null) {
      return Object.entries(result).map(([key, value]) => ({
        value: key,
        label: value
      }));
    }
    return [];
  }
  
  return result;
});

// Filter options based on search query
const filterOptions = () => {
  try {
    const query = searchQuery.value.toLowerCase();
    
    if (!query) {
      filteredOptions.value = options.value;
    } else {
      filteredOptions.value = options.value.filter(option => {
        const label = typeof option === 'string' ? option : option.label || option.value;
        return label.toLowerCase().includes(query);
      });
    }
  } catch (error) {
    console.error('Error filtering options:', error);
    filteredOptions.value = [];
  }
};

// Watch for changes in options and search query
watch([options, searchQuery], filterOptions, { immediate: true });

// Handle input changes
function handleInput(e) {
  searchQuery.value = e.target.value;
  isOpen.value = true;
  focusedIndex.value = -1;
}

// Handle option selection
function selectOption(option) {
  const value = typeof option === 'string' ? option : option.value;
  const label = typeof option === 'string' ? option : option.label || option.value;
  
  if (!selectedOptions.value.find(item => item.value === value)) {
    selectedOptions.value.push({ value, label });
    updateNodeValue();
  }
  
  searchQuery.value = '';
  isOpen.value = false;
  focusedIndex.value = -1;
}

// Remove selected option
function removeOption(index) {
  selectedOptions.value.splice(index, 1);
  updateNodeValue();
}

// Update FormKit node value
function updateNodeValue() {
  props.context.node.input(selectedOptions.value);
}

// Handle keyboard navigation
function handleKeydown(e) {
  if (!isOpen.value) {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      isOpen.value = true;
    }
    return;
  }

  switch (e.key) {
    case 'ArrowDown':
      e.preventDefault();
      focusedIndex.value = Math.min(focusedIndex.value + 1, filteredOptions.value.length - 1);
      break;
    case 'ArrowUp':
      e.preventDefault();
      focusedIndex.value = Math.max(focusedIndex.value - 1, -1);
      break;
    case 'Enter':
      e.preventDefault();
      if (focusedIndex.value >= 0 && filteredOptions.value[focusedIndex.value]) {
        selectOption(filteredOptions.value[focusedIndex.value]);
      }
      break;
    case 'Escape':
      isOpen.value = false;
      focusedIndex.value = -1;
      break;
    case 'Backspace':
      if (!searchQuery.value && selectedOptions.value.length > 0) {
        removeOption(selectedOptions.value.length - 1);
      }
      break;
  }
}

// Calculate dropdown position
function calculateDropdownPosition() {
  if (inputRef.value) {
    const rect = inputRef.value.getBoundingClientRect();
    dropdownStyle.value = {
      position: 'fixed',
      top: `${rect.bottom + 4}px`,
      left: `${rect.left}px`,
      width: `${rect.width}px`,
      zIndex: 15
    };
  }
}

// Handle focus
function handleFocus() {
  isOpen.value = true;
  nextTick(() => {
    calculateDropdownPosition();
  });
}

// Handle blur
function handleBlur() {
  setTimeout(() => {
    isOpen.value = false;
    focusedIndex.value = -1;
  }, 150);
}

// Handle click outside
function handleClickOutside(event) {
  if (inputRef.value && !inputRef.value.contains(event.target)) {
    isOpen.value = false;
    focusedIndex.value = -1;
  }
}

// Initialize selected options from context value
watch(() => props.context._value, (newValue) => {
  if (newValue && Array.isArray(newValue)) {
    selectedOptions.value = newValue;
  }
}, { immediate: true });

// Recalculate position when window resizes or scrolls
onMounted(() => {
  window.addEventListener('resize', () => {
    if (isOpen.value) {
      calculateDropdownPosition();
    }
  });
  
  // Add scroll listener to update dropdown position
  window.addEventListener('scroll', () => {
    if (isOpen.value) {
      // Use requestAnimationFrame for smooth updates
      requestAnimationFrame(() => {
        calculateDropdownPosition();
      });
    }
  }, true); // Use capture phase to catch all scroll events
  
  // Add click outside listener
  document.addEventListener('click', handleClickOutside);
});

onUnmounted(() => {
  window.removeEventListener('resize', calculateDropdownPosition);
  window.removeEventListener('scroll', calculateDropdownPosition, true);
  document.removeEventListener('click', handleClickOutside);
});
</script>

<template>
  <div ref="inputRef" class="relative w-full border border-[rgb(var(--fk-border-color))] rounded-lg overflow-hidden focus-within:border-primary min-h-[2.5rem]">
    <!-- Selected Options Display -->
    <div class="flex flex-wrap gap-1 p-2 min-h-[2.5rem]">
      <div
        v-for="(option, index) in selectedOptions"
        :key="option.value"
        class="flex items-center gap-1 px-2 py-1 bg-primary text-white rounded-md text-sm"
      >
        <span>{{ option.label }}</span>
        <button
          type="button"
          @click="removeOption(index)"
          class="ml-1 hover:bg-white hover:bg-opacity-20 rounded-full w-4 h-4 flex items-center justify-center"
        >
          ×
        </button>
      </div>
      
      <!-- Search Input -->
      <input
        :value="searchQuery"
        @input="handleInput"
        @focus="handleFocus"
        @blur="handleBlur"
        @keydown="handleKeydown"
        :placeholder="selectedOptions.length === 0 ? context.attrs.placeholder || 'Search options...' : ''"
        class="flex-1 min-w-0 border-none outline-none bg-transparent px-3 text-sm placeholder-[rgb(var(--fk-placeholder-color))]"
      />
    </div>

    <!-- Dropdown Arrow -->
    <div class="absolute right-2 top-[12px] transform -translate-y-1/2 pointer-events-none">
      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
      </svg>
    </div>
  </div>

  <!-- Dropdown Options - Moved outside container -->
  <Teleport to="body">
    <div
      v-if="isOpen"
      class="fixed z-[15] bg-white border border-gray-300 rounded-md shadow-lg max-h-60 overflow-y-auto"
      :style="dropdownStyle"
    >
      <div
        v-if="filteredOptions.length === 0"
        class="px-3 py-2 text-gray-500 text-sm"
      >
        No options found
      </div>
      
      <div
        v-for="(option, index) in filteredOptions"
        :key="typeof option === 'string' ? option : option.value"
        @click="selectOption(option)"
        @mouseenter="focusedIndex = index"
        :class="[
          'px-3 py-2 cursor-pointer text-sm hover:bg-gray-100',
          focusedIndex === index ? 'bg-gray-100' : '',
          selectedOptions.find(item => item.value === (typeof option === 'string' ? option : option.value)) 
            ? 'bg-primary bg-opacity-10 text-primary' 
            : ''
        ]"
      >
        {{ typeof option === 'string' ? option : option.label || option.value }}
      </div>
    </div>
  </Teleport>
</template> 