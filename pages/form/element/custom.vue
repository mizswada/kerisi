<script setup>
definePageMeta({
  title: "Custom Input",
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
      <template #header> One-Time Password </template>
      <template #body>
        <FormKit
          type="otp"
          label="One-Time Password"
          help="We've sent code to your phone number"
          digits="6"
        />
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('otp')"
          >
            {{ showCode.otp ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.otp" class="relative" v-highlight>
              <button
                @click="copyCode('codeOtp')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeOtp']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeOtp"] }}
              </span>
              <NuxtScrollbar style="height: 200px">
                <pre id="codeOtp" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="otp"
                        label="One-Time Password"
                        help="We've sent code to your phone number"
                        digits="6"
                      /&gt;
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

    <rs-card>
      <template #header> Dropzone</template>
      <template #body>
        <FormKit
          type="dropzone"
          label="Documents"
          help="Select as many documents as you would like."
          multiple="true"
        />
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('dropzone')"
          >
            {{ showCode.dropzone ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.dropzone" class="relative" v-highlight>
              <button
                @click="copyCode('codeDropzone')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDropzone']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDropzone"] }}
              </span>
              <NuxtScrollbar style="height: 200px">
                <pre id="codeDropzone" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="dropzone"
                        label="Documents"
                        help="Select as many documents as you would like."
                        multiple="true"
                      /&gt;
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

    <rs-card>
      <template #header> Range</template>
      <template #body>
        <FormKit
          type="range"
          label="Volume"
          min="0"
          max="11"
          help="Select your volume level."
        />
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('range')"
          >
            {{ showCode.range ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.range" class="relative" v-highlight>
              <button
                @click="copyCode('codeRange')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeRange']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeRange"] }}
              </span>
              <NuxtScrollbar style="height: 200px">
                <pre id="codeRange" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="range"
                        label="Volume"
                        min="0"
                        max="11"
                        help="Select your volume level."
                      /&gt;
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

    <rs-card>
      <template #header> Color</template>
      <template #body>
        <FormKit
          type="color"
          value="#FB7185"
          label="Select a color"
          help="Select your favorite color."
        />
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('color')"
          >
            {{ showCode.color ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.color" class="relative" v-highlight>
              <button
                @click="copyCode('codeColor')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeColor']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeColor"] }}
              </span>
              <NuxtScrollbar style="height: 200px">
                <pre id="codeColor" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="color"
                        value="#FB7185"
                        label="Select a color"
                        help="Select your favorite color."
                      /&gt;
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
