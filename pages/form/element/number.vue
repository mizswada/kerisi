<script setup>
definePageMeta({
  title: "Input Number",
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
        <FormKit
          type="number"
          help="What temperature should the house be?"
          label="Temperature"
          value="25"
          step="1"
        />
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
              <NuxtScrollbar style="height: 200px">
                <pre id="codeDefault" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit type="number" label="Temperature" value="25" step="1"/&gt;
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
      <template #header> Number validation </template>
      <template #body>
        <FormKit
          type="number"
          help="The temperature should be between 20 - 50 degrees"
          label="Temperature"
          value="20"
          step="1"
          validation="between:20,50"
          validation-visibility="live"
        />

        <FormKit
          type="number"
          help="Minimum temperature should be 20 degrees"
          label="Temperature"
          value="20"
          step="1"
          validation="min:20"
          validation-visibility="live"
        />

        <FormKit
          type="number"
          help="Maximum temperature should be 50 degrees"
          label="Temperature"
          value="50"
          step="1"
          validation="max:50"
          validation-visibility="live"
        />

        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('validation')"
          >
            {{ showCode.validation ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.validation" class="relative" v-highlight>
              <button
                @click="copyCode('codeValidation')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeValidation']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeValidation"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeValidation" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;

                      &lt;!-- Number between 20 - 50 validation --&gt;
                      &lt;FormKit 
                        type="number" 
                        label="Temperature" 
                        value="20" 
                        step="1" 
                        validation="between:20,50" 
                        validation-visibility="live"
                      /&gt;

                      &lt;!-- Number minimum 20 --&gt;
                      &lt;FormKit 
                        type="number" 
                        label="Temperature" 
                        value="20" 
                        step="1" 
                        validation="min:20" 
                        validation-visibility="live"
                      /&gt;

                      &lt;!-- Number maximum 50 --&gt;
                      &lt;FormKit 
                        type="number" 
                        label="Temperature" 
                        value="50" 
                        step="1" 
                        validation="max:50" 
                        validation-visibility="live"
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
  