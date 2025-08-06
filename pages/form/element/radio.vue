<script setup>
definePageMeta({
  title: "Input Radio",
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
      <template #header> Default </template>
      <template #body>
        <FormKit
          type="radio"
          label="Cars"
          help="What is your favourite car?"
          :options="options"
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
              <NuxtScrollbar style="height: 400px">
                <pre id="codeDefault" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit
                        type="radio"
                        label="Cars"
                        help="What is your favourite car?"
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
      <template #header> Disabled Radio Item </template>
      <template #body>
        <FormKit
          type="radio"
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
                        type="radio"
                        label="Movies"
                        help="What is your favourite movie?"
                        :options="options2"
                      /&gt;
                    &lt;/template&gt;
  
                    &lt;script setup&gt;
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
