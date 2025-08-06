<script setup>
definePageMeta({
  title: "Vue Select",
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

const countryList = [
  "United States",
  "Canada",
  "United Kingdom",
  "Germany",
  "France",
  "Japan",
  "Australia",
  "Brazil",
  "India",
  "China",
];
const emptyList = [];
const selected = ref([]);
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header> Info </template>
      <template #body>
        <p class="text-justify">
          <a
            href="https://vue-select.org"
            target="_blank"
            class="hover:underline text-primary"
            >Vue Select</a
          >
          is a feature rich select/dropdown/typeahead component. It provides a
          default template that fits most use cases for a filterable select
          dropdown. The component is designed to be as lightweight as possible,
          while maintaining high standards for accessibility, developer
          experience, and customization.
        </p>
      </template>
    </rs-card>

    <rs-card>
      <template #header> Example </template>
      <template #body>
        <div class="mb-4">
          <label
            for="country"
            class="text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm"
            >Single Select Country</label
          >
          <v-select name="country" :options="countryList"></v-select>
        </div>

        <div class="mb-4">
          <label
            for="country"
            class="text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm"
            >Multiple Select Country</label
          >
          <v-select name="country" :options="countryList" multiple></v-select>
        </div>
        <div class="mb-4">
          <label
            for="country"
            class="text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm"
            >Taggable Select Country</label
          >
          <v-select
            name="country"
            :options="emptyList"
            multiple
            taggable
          ></v-select>
        </div>

        <div class="mb-4">
          <label
            for="country"
            class="text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm"
            >Pushable Tag Select Country</label
          >
          <v-select
            name="country"
            :options="emptyList"
            multiple
            taggable
            push-tags
          ></v-select>
        </div>
        <div class="flex justify-end">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('example')"
          >
            {{ showCode.example ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.example" class="relative" v-highlight>
              <button
                @click="copyCode('codeExample')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeExample']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeExample"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeExample" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;!-- Single Select Country --&gt;
                      &lt;v-select name="country" :options="countryList"&gt;&lt;/v-select&gt;
                      
                      &lt;!-- Multiple Select Country --&gt;
                      &lt;v-select name="country" :options="countryList" multiple&gt;&lt;/v-select&gt;

                      &lt;!-- Taggable Select Country --&gt;
                      &lt;v-select
                        name="country"
                        :options="emptyList"
                        multiple
                        taggable
                      &gt;&lt;/v-select&gt;
                      
                      &lt;!-- Pushable Tag Select Country --&gt;
                      &lt;v-select
                          name="country"
                          :options="emptyList"
                          multiple
                          taggable
                          push-tags
                      &gt;&lt;/v-select&gt;
                    &lt;/template&gt;
                      
                    &lt;script setup&gt;
                    const countryList = [
                      "United States",
                      "Canada",
                      "United Kingdom",
                      "Germany",
                      "France",
                      "Japan",
                      "Australia",
                      "Brazil",
                      "India",
                      "China"
                    ];
                    const emptyList = [];
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
        <label
          for="country"
          class="text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm"
          >Limit Select</label
        >
        <v-select
          name="country"
          class="mb-4"
          v-model="selected"
          :selectable="() => selected.length < 3"
          :options="countryList"
          multiple
        ></v-select>
        <div class="flex justify-end">
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
                      &lt;v-select 
                        name="country"
                        v-model="selected"
                        :selectable="() => selected.length &lt; 3"
                        :options="countryList"
                        multiple
                      &gt;&lt;/v-select&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;
                    const countryList = [
                      "United States",
                      "Canada",
                      "United Kingdom",
                      "Germany",
                      "France",
                      "Japan",
                      "Australia",
                      "Brazil",
                      "India",
                      "China"
                    ];
                    const selected = ref([]);
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
