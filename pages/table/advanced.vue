<script setup>
import data from "./data.js";

definePageMeta({
  title: "Table Advanced",
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
      <template #header> Advanced</template>
      <template #body>
        <rs-table
          :data="data"
          :options="{
            variant: 'default',
            striped: true,
            borderless: true,
          }"
          :options-advanced="{
            sortable: true,
            responsive: true,
            filterable: false,
          }"
          advanced
        >
          <template v-slot:status="data">
            <rs-badge
              :variant="
                data.text === 'Active'
                  ? 'success'
                  : data.text == 'Inactive'
                  ? 'warning'
                  : 'danger'
              "
            >
              {{ data.text }}
            </rs-badge>
          </template>
        </rs-table>
        <div class="flex justify-end mt-2">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('advanced')"
          >
            {{ showCode.advanced ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.advanced" class="relative" v-highlight>
              <button
                @click="copyCode('codeAdvanced')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeAdvanced']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeAdvanced"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeAdvanced" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;rs-table
                        :data="data"
                        :options="{
                          variant: 'default',
                          striped: true,
                          borderless: true,
                        }"
                        :options-advanced="{
                          sortable: true,
                          responsive: true,
                          filterable: false,
                        }"
                        advanced
                      &gt;
                        &lt;template v-slot:status="data"&gt;
                          &lt;rs-badge
                            :variant="
                              data.text === 'Active'
                                ? 'success'
                                : data.text == 'Inactive'
                                ? 'warning'
                                : 'danger'
                            "
                          &gt;
                            {{ data.text }}
                          &lt;/rs-badge&gt;
                        &lt;/template&gt;
                      &lt;/rs-table&gt;
                    &lt;/template&gt;
    
                    &lt;script setup&gt;
                      import data from "./data.js";
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
