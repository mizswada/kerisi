<script setup>
definePageMeta({
  title: "Floating Chat",
  breadcrumb: [
    {
      name: "Components",
      type: "current",
    },
  ],
});

const showCode = reactive({});
const tooltips = reactive({});

const toggleCode = (section) => {
  showCode[section] = !showCode[section];
};

const copyCode = (codeId) => {
  const codeElement = document.getElementById(codeId);
  if (codeElement) {
    navigator.clipboard
      .writeText(codeElement.textContent)
      .then(() => {
        console.log("Code copied to clipboard");
        showTooltip(codeId, "Code copied!");
      })
      .catch((err) => {
        console.error("Failed to copy code: ", err);
        showTooltip(codeId, "Failed to copy code");
      });
  }
};

const showTooltip = (codeId, message) => {
  tooltips[codeId] = message;
  setTimeout(() => {
    tooltips[codeId] = null;
  }, 2000);
};

// Floating chat states
const showChat = ref(true);
const chatBadge = ref(3);
const chatPosition = ref('bottom-right');
const chatSize = ref('md');
const chatBadgeColor = ref('danger');
const useImage = ref(true);
const chatImage = ref('/img/chat-avatar.svg');
const chatImageAlt = ref('Chat Support');

const handleChatClick = () => {
  console.log('Floating chat clicked!');
  // You can implement your chat logic here
  // For example: open modal, navigate to chat page, etc.
};

const toggleChat = () => {
  showChat.value = !showChat.value;
};

const updateBadge = () => {
  chatBadge.value = Math.floor(Math.random() * 10) + 1;
};

const positions = [
  { label: 'Bottom Right', value: 'bottom-right' },
  { label: 'Bottom Left', value: 'bottom-left' },
  { label: 'Top Right', value: 'top-right' },
  { label: 'Top Left', value: 'top-left' },
];

const sizes = [
  { label: 'Small', value: 'sm' },
  { label: 'Medium', value: 'md' },
  { label: 'Large', value: 'lg' },
];

const badgeColors = [
  { label: 'Primary', value: 'primary' },
  { label: 'Secondary', value: 'secondary' },
  { label: 'Success', value: 'success' },
  { label: 'Danger', value: 'danger' },
  { label: 'Warning', value: 'warning' },
  { label: 'Info', value: 'info' },
];
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header>
        <div class="flex">
          <Icon class="mr-2 flex justify-center" name="ic:outline-info"></Icon>
          Floating Chat Component
        </div>
      </template>
      <template #body>
        <p class="mb-4">
          The floating chat component provides a modern, responsive chat button that can be positioned anywhere on the screen. 
          It includes features like badges, different sizes, positions, and smooth animations.
        </p>
      </template>
    </rs-card>

    <!-- Basic Usage -->
    <rs-card>
      <template #header>Basic Usage</template>
      <template #body>
        <div class="mb-4">
          <p class="text-sm text-gray-600 mb-4">
            Basic floating chat component with default settings.
          </p>
          
                     <div class="flex flex-wrap gap-4 mb-4">
             <rs-button @click="toggleChat" variant="primary">
               {{ showChat ? 'Hide' : 'Show' }} Chat
             </rs-button>
             <rs-button @click="updateBadge" variant="secondary">
               Update Badge
             </rs-button>
             <rs-button @click="useImage = !useImage" variant="info">
               {{ useImage ? 'Use Icon' : 'Use Image' }}
             </rs-button>
           </div>
        </div>

        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('basic')"
          >
            {{ showCode.basic ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.basic" class="relative" v-highlight>
              <button
                @click="copyCode('codeBasic')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy Code
              </button>
              <span
                v-if="tooltips['codeBasic']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeBasic"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeBasic" class="language-html shadow-none">
                                     <code>
&lt;template&gt;
  &lt;RsFloatingChat
    :show="showChat"
    :badge="chatBadge"
    badge-color="danger"
    position="bottom-right"
    size="md"
    :use-image="true"
    image="/img/chat-avatar.svg"
    image-alt="Chat Support"
    tooltip="Chat with us"
    @click="handleChatClick"
  /&gt;
&lt;/template&gt;

&lt;script setup&gt;
const showChat = ref(true);
const chatBadge = ref(3);

const handleChatClick = () => {
  console.log('Floating chat clicked!');
};
&lt;/script&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <!-- Configuration Options -->
    <rs-card>
      <template #header>Configuration Options</template>
      <template #body>
                 <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mb-4">
           <div>
             <label class="block text-sm font-medium text-gray-700 mb-2">Position</label>
             <FormKit
               type="select"
               v-model="chatPosition"
               :options="positions"
               placeholder="Select position"
             />
           </div>
           
           <div>
             <label class="block text-sm font-medium text-gray-700 mb-2">Size</label>
             <FormKit
               type="select"
               v-model="chatSize"
               :options="sizes"
               placeholder="Select size"
             />
           </div>
           
           <div>
             <label class="block text-sm font-medium text-gray-700 mb-2">Badge Color</label>
             <FormKit
               type="select"
               v-model="chatBadgeColor"
               :options="badgeColors"
               placeholder="Select badge color"
             />
           </div>
           
           <div>
             <label class="block text-sm font-medium text-gray-700 mb-2">Badge Count</label>
             <FormKit
               type="number"
               v-model="chatBadge"
               placeholder="Enter badge count"
             />
           </div>
           
           <div>
             <label class="block text-sm font-medium text-gray-700 mb-2">Image URL</label>
             <FormKit
               type="text"
               v-model="chatImage"
               placeholder="Enter image URL"
             />
           </div>
           
           <div>
             <label class="block text-sm font-medium text-gray-700 mb-2">Image Alt Text</label>
             <FormKit
               type="text"
               v-model="chatImageAlt"
               placeholder="Enter alt text"
             />
           </div>
         </div>

        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('config')"
          >
            {{ showCode.config ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.config" class="relative" v-highlight>
              <button
                @click="copyCode('codeConfig')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy Code
              </button>
              <span
                v-if="tooltips['codeConfig']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeConfig"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeConfig" class="language-html shadow-none">
                                     <code>
&lt;template&gt;
  &lt;RsFloatingChat
    :show="showChat"
    :badge="chatBadge"
    :badge-color="chatBadgeColor"
    :position="chatPosition"
    :size="chatSize"
    :use-image="useImage"
    :image="chatImage"
    :image-alt="chatImageAlt"
    tooltip="Chat with us"
    @click="handleChatClick"
  /&gt;
&lt;/template&gt;

&lt;script setup&gt;
const showChat = ref(true);
const chatBadge = ref(3);
const chatPosition = ref('bottom-right');
const chatSize = ref('md');
const chatBadgeColor = ref('danger');
const useImage = ref(true);
const chatImage = ref('/img/chat-avatar.svg');
const chatImageAlt = ref('Chat Support');

const handleChatClick = () => {
  console.log('Floating chat clicked!');
};
&lt;/script&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <!-- Props Reference -->
    <rs-card>
      <template #header>Props Reference</template>
      <template #body>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Prop</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Type</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Default</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Description</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">position</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">'bottom-right'</td>
                <td class="px-6 py-4 text-sm text-gray-500">Position of the floating chat (bottom-right, bottom-left, top-right, top-left)</td>
              </tr>
              <tr>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">size</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">'md'</td>
                <td class="px-6 py-4 text-sm text-gray-500">Size of the floating chat (sm, md, lg)</td>
              </tr>
              <tr>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">show</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Boolean</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">true</td>
                <td class="px-6 py-4 text-sm text-gray-500">Whether to show the floating chat</td>
              </tr>
              <tr>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">badge</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String/Number</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">null</td>
                <td class="px-6 py-4 text-sm text-gray-500">Badge count to display on the chat button</td>
              </tr>
              <tr>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">badgeColor</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">'danger'</td>
                <td class="px-6 py-4 text-sm text-gray-500">Color of the badge (primary, secondary, success, danger, warning, info)</td>
              </tr>
              <tr>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">tooltip</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">'Chat with us'</td>
                <td class="px-6 py-4 text-sm text-gray-500">Tooltip text to display on hover</td>
              </tr>
                             <tr>
                 <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">onClick</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Function</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">() => {}</td>
                 <td class="px-6 py-4 text-sm text-gray-500">Function to call when chat button is clicked</td>
               </tr>
               <tr>
                 <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">useImage</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">Boolean</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">false</td>
                 <td class="px-6 py-4 text-sm text-gray-500">Whether to use image instead of icon</td>
               </tr>
               <tr>
                 <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">image</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">null</td>
                 <td class="px-6 py-4 text-sm text-gray-500">URL of the image to display</td>
               </tr>
               <tr>
                 <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">imageAlt</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">'Chat'</td>
                 <td class="px-6 py-4 text-sm text-gray-500">Alt text for the image</td>
               </tr>
               <tr>
                 <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">imageSize</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">String</td>
                 <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">'auto'</td>
                 <td class="px-6 py-4 text-sm text-gray-500">Size of the image (auto, sm, md, lg, or specific size)</td>
               </tr>
            </tbody>
          </table>
        </div>
      </template>
    </rs-card>

    <!-- Events Reference -->
    <rs-card>
      <template #header>Events Reference</template>
      <template #body>
        <div class="overflow-x-auto">
          <table class="min-w-full divide-y divide-gray-200">
            <thead class="bg-gray-50">
              <tr>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Event</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Payload</th>
                <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Description</th>
              </tr>
            </thead>
            <tbody class="bg-white divide-y divide-gray-200">
              <tr>
                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">click</td>
                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">-</td>
                <td class="px-6 py-4 text-sm text-gray-500">Emitted when the chat button is clicked</td>
              </tr>
            </tbody>
          </table>
        </div>
      </template>
    </rs-card>
  </div>

  <!-- Demo Floating Chat -->
  <RsFloatingChat
    :show="showChat"
    :badge="chatBadge"
    :badge-color="chatBadgeColor"
    :position="chatPosition"
    :size="chatSize"
    :use-image="useImage"
    :image="chatImage"
    :image-alt="chatImageAlt"
    tooltip="Chat with us"
    @click="handleChatClick"
  />
</template>
