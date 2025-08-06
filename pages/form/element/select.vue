<script setup>
definePageMeta({
  title: "Input Select",
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

const options = [
  "Malaysia",
  "Singapore",
  "Thailand",
  "Indonesia",
  "Brunei",
  "Laos",
  "Cambodia",
  "Myanmar",
  "Philippines",
  "Vietnam",
  "Timor-Leste",
];

const options2 = [
  { label: "Malaysia", value: "my", attrs: { disabled: true } },
  { label: "Singapore", value: "sg", attrs: { disabled: true } },
  { label: "Thailand", value: "th", attrs: { disabled: true } },
  { label: "Indonesia", value: "id", attrs: { disabled: true } },
  { label: "Brunei", value: "b", attrs: { disabled: true } },
  { label: "Laos", value: "ls", attrs: { disabled: true } },
  { label: "Cambodia", value: "cb", attrs: { disabled: true } },
  { label: "Japan", value: "jp" },
  { label: "Myanmar", value: "mm", attrs: { disabled: true } },
];
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header> Single Select </template>
      <template #body>
        <FormKit
          type="select"
          label="Which country is the biggest?"
          :options="options"
        />
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('single')"
          >
            {{ showCode.single ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.single" class="relative" v-highlight>
              <button
                @click="copyCode('codeSingle')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeSingle']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeSingle"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeSingle" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="select"
                        label="Which country is the biggest?"
                        :options="options"
                      /&gt;
                    &lt;/template&gt;
    
                    &lt;script setup&gt;
                      const options = [
                        "Malaysia",
                        "Singapore",
                        "Thailand",
                        "Indonesia",
                        "Brunei",
                        "Laos",
                        "Cambodia",
                        "Myanmar",
                        "Philippines",
                        "Vietnam",
                        "Timor-Leste",
                      ];
                    &lt;/script&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <rs-card>
      <template #header> Multiple Select </template>
      <template #body>
        <FormKit
          type="select"
          multiple
          label="Select all the country that you like?"
          help="Select all that apply by holding command (macOS) or control (PC)."
          :options="options"
          input-class="h-full"
        />
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('multiple')"
          >
            {{ showCode.multiple ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.multiple" class="relative" v-highlight>
              <button
                @click="copyCode('codeMultiple')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeMultiple']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeMultiple"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeMultiple" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="select"
                        multiple
                        label="Select all the country that you like?"
                        help="Select all that apply by holding command (macOS) or control (PC)."
                        :options="options"
                        input-class="h-full"
                      /&gt;
                    &lt;/template&gt;
    
                    &lt;script setup&gt;
                      const options = [
                        "Malaysia",
                        "Singapore",
                        "Thailand",
                        "Indonesia",
                        "Brunei",
                        "Laos",
                        "Cambodia",
                        "Myanmar",
                        "Philippines",
                        "Vietnam",
                        "Timor-Leste",
                      ];
                    &lt;/script&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <rs-card>
      <template #header> Disabled Select Item </template>
      <template #body>
        <FormKit
          type="select"
          label="Which country you want to visit?"
          :options="options2"
        />
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('disabled')"
          >
            {{ showCode.disabled ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.disabled" class="relative" v-highlight>
              <button
                @click="copyCode('codeDisabled')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDisabled']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDisabled"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeDisabled" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="select"
                        label="Which country you want to visit?"
                        :options="options2"
                      /&gt;
                    &lt;/template&gt;
    
                    &lt;script setup&gt;
                      const options2 = [
                        { label: "Malaysia", value: "my", attrs: { disabled: true } },
                        { label: "Singapore", value: "sg", attrs: { disabled: true } },
                        { label: "Thailand", value: "th", attrs: { disabled: true } },
                        { label: "Indonesia", value: "id", attrs: { disabled: true } },
                        { label: "Brunei", value: "b", attrs: { disabled: true } },
                        { label: "Laos", value: "ls", attrs: { disabled: true } },
                        { label: "Cambodia", value: "cb", attrs: { disabled: true } },
                        { label: "Japan", value: "jp" },
                        { label: "Myanmar", value: "mm", attrs: { disabled: true } },
                      ];
                    &lt;/script&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <rs-card>
      <template #header> Validation </template>
      <template #body>
        <FormKit
          type="select"
          label="Which country is the biggest?"
          placeholder="Select a country"
          :options="options"
          validation="required|is:Indonesia"
          validation-visibility="dirty"
          :validation-messages="{
            is: 'Nope! Indonesia is the largest country in the list.',
          }"
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
                      &lt;FormKit
                        type="select"
                        label="Which country is the biggest?"
                        placeholder="Select a country"
                        :options="options"
                        validation="required|is:Indonesia"
                        validation-visibility="dirty"
                        :validation-messages="{
                          is: 'Nope! Indonesia is the largest country in the list.',
                        }"
                      /&gt;
                    &lt;/template&gt;
    
                    &lt;script setup&gt;
                      const options = [
                        "Malaysia",
                        "Singapore",
                        "Thailand",
                        "Indonesia",
                        "Brunei",
                        "Laos",
                        "Cambodia",
                        "Myanmar",
                        "Philippines",
                        "Vietnam",
                        "Timor-Leste",
                      ];
                    &lt;/script&gt;
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
