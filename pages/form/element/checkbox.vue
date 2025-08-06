<script setup>
definePageMeta({
  title: "Input Checkbox",
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

const options = ["Audi", "Mazda", "Proton", "Perodua", "BMW", "Mercedes"];
const options2 = [
  { label: "Shawshank redemption", value: "shawshank" },
  { label: "The Godfather", value: "godfather" },
  {
    label: "Casablanca (too old)",
    value: "casablanca",
    attrs: { disabled: true },
  },
  { label: "The Lord of the Rings", value: "lotr" },
  { label: "Saving Private Ryan", value: "saving-ryan" },
];
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header> Single Checkbox </template>
      <template #body>
        <FormKit
          type="checkbox"
          label="Terms and Conditions"
          help="Do you agree to our terms of service?"
          name="terms"
          validation="accepted"
          validation-visibility="dirty"
          input-class="icon-check"
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
                        type="checkbox"
                        label="Terms and Conditions"
                        help="Do you agree to our terms of service?"
                        name="terms"
                        validation="accepted"
                        validation-visibility="dirty"
                        input-class="icon-check"
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
      <template #header> Multiple Checkbox </template>
      <template #body>
        <FormKit
          type="checkbox"
          label="Cars"
          help="Select your favourite cars?"
          :options="options"
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
                        type="checkbox"
                        label="Cars"
                        help="Select your favourite cars?"
                        :options="options"
                      /&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;
                      const options = ["Audi", "Mazda", "Proton", "Perodua", "BMW", "Mercedes"];
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
      <template #header> Disabled Checkbox Item </template>
      <template #body>
        <FormKit
          type="checkbox"
          label="Movies"
          help="What is your favourite movie?"
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
                        type="checkbox"
                        label="Movies"
                        help="What is your favourite movie?"
                        :options="options"
                      /&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;
                      const options = [
                        { label: "Shawshank redemption", value: "shawshank" },
                        { label: "The Godfather", value: "godfather" },
                        {
                          label: "Casablanca (too old)",
                          value: "casablanca",
                          attrs: { disabled: true },
                        },
                        { label: "The Lord of the Rings", value: "lotr" },
                        { label: "Saving Private Ryan", value: "saving-ryan" },
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
          type="checkbox"
          label="Cars"
          help="Please select at least 3 cars?"
          :options="options"
          validation="required|min:3"
          validation-visibility="dirty"
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
                        type="checkbox"
                        label="Cars"
                        help="Please select at least 3 cars?"
                        :options="options"
                        validation="required|min:3"
                        validation-visibility="dirty"
                      /&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;
                      const options = ["Audi", "Mazda", "Proton", "Perodua", "BMW", "Mercedes"];
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
