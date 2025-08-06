<script setup>
definePageMeta({
  title: "Progress Bar",
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
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header> Default </template>
      <template #body>
        <p class="mb-4">
          Use the <code>rs-progress-bar</code> component to create a progress
          bar. There are props to change the color, size, max, show value, and
          value.
        </p>
        <rs-progress-bar
          label="Primary Progress"
          :value="11"
          :max="100"
          :variant="'primary'"
          :size="'md'"
          :show-value="true"
        ></rs-progress-bar>
        <rs-progress-bar
          label="Secondary Progress"
          :value="43"
          :max="100"
          :variant="'secondary'"
          :size="'md'"
          :show-value="true"
        ></rs-progress-bar>
        <rs-progress-bar
          label="Success Progress"
          :value="45"
          :max="100"
          :variant="'success'"
          :size="'md'"
          :show-value="true"
        ></rs-progress-bar>
        <rs-progress-bar
          label="Danger Progress"
          :value="90"
          :max="100"
          :variant="'danger'"
          :size="'md'"
          :show-value="true"
        ></rs-progress-bar>
        <rs-progress-bar
          label="Warning Progress"
          :value="36"
          :max="100"
          :variant="'warning'"
          :size="'md'"
          :show-value="true"
        ></rs-progress-bar>
        <rs-progress-bar
          label="Info Progress"
          :value="25"
          :max="100"
          :variant="'info'"
          :size="'md'"
          :show-value="true"
        ></rs-progress-bar>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('default')"
          >
            {{ showCode.default ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.default" class="relative" v-highlight>
              <button
                @click="copyCode('codeDefault')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDefault']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDefault"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeDefault" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;rs-progress-bar 
                          label="Primary Progress"
                          :value="11"
                          :max="100"
                          variant="primary|secondary|success|danger|warning|info"
                          size="md|sm|lg"
                          :show-value="true|false"
                      &gt;&lt;/rs-progress-bar&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;&lt;/script&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>
  </div>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
