<script setup>
import { DateTime } from "luxon";
import { reactive } from "vue";

definePageMeta({
  title: "Date Time",
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

const dt = DateTime.now();

const date = dt.toISODate();
const datetime = dt.toFormat("yyyy-MM-dd'T'HH:mm");
const month = dt.toFormat("yyyy-MM");
const time = dt.toFormat("HH:mm");
const week = dt.toFormat("yyyy-'W'kk");
const dateStart = dt.plus({ days: -7 }).toFormat("yyyy-MM-dd");
const dateEnd = dt.plus({ days: 7 }).toFormat("yyyy-MM-dd");
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header> Default </template>
      <template #body>
        <FormKit type="datetime-local" :value="datetime" label="Datetime" />
        <FormKit type="date" :value="date" label="Date Only" />
        <FormKit type="month" :value="month" label="Month Only" />
        <FormKit type="time" :value="time" label="Time Only" />
        <FormKit type="week" :value="week" label="Week Only" />
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
                      &lt;!-- Date Time --&gt;
                      &lt;FormKit type="datetime-local" :value="datetime" label="Datetime" /&gt;

                      &lt;!-- Date Only --&gt;
                      &lt;FormKit type="date" :value="date" label="Date Only" /&gt;

                      &lt;!-- Month Only --&gt;
                      &lt;FormKit type="month" :value="month" label="Month Only" /&gt;

                      &lt;!-- Time Only --&gt;
                      &lt;FormKit type="time" :value="time" label="Time Only" /&gt;

                      &lt;!-- Week Only --&gt;
                      &lt;FormKit type="week" :value="week" label="Week Only" /&gt;
                    &lt;/template&gt;
        
                    &lt;script setup&gt;
                      import { DateTime } from "luxon";
        
                      const dt = DateTime.now();
        
                      const date = dt.toISODate();
                      const datetime = dt.toFormat("yyyy-MM-dd'T'HH:mm");
                      const month = dt.toFormat("yyyy-MM");
                      const time = dt.toFormat("HH:mm");
                      const week = dt.toFormat("yyyy-'W'kk");
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
          type="date"
          label="Date"
          help="Enter date (the date must be after 2020)"
          validation="date_after:2020-12-31"
          validation-visibility="live"
        />
        <FormKit
          type="date"
          label="Date"
          help="Enter date (the date must be before 2020)"
          validation="date_before:2020-01-01"
          validation-visibility="live"
        />
        <FormKit
          type="date"
          label="Date"
          help="Enter date (the date must be between 6/6/2022 and 20/6/2022)"
          :validation="[['date_between', dateStart, dateEnd]]"
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
                      &lt;!-- Date After --&gt;
                      &lt;FormKit
                        type="date"
                        label="Date"
                        help="Enter date (the date must be after 2020)"
                        validation="date_after:2020-12-31"
                        validation-visibility="live"
                      /&gt;

                      &lt;!-- Date Before --&gt;
                      &lt;FormKit
                        type="date"
                        label="Date"
                        help="Enter date (the date must be before 2020)"
                        validation="date_before:2020-01-01"
                        validation-visibility="live"
                      /&gt;

                      &lt;!-- Date Between --&gt;
                      &lt;FormKit
                        type="date"
                        label="Date"
                        help="Enter date (the date must be between 6/6/2022 and 20/6/2022)"
                        :validation="[['date_between', dateStart, dateEnd]]"
                        validation-visibility="live"
                      /&gt;
                    &lt;/template&gt;
        
                    &lt;script setup&gt;
                      import { DateTime } from "luxon";
        
                      const dt = DateTime.now();
        
                      const dateStart = dt.plus({ days: -7 }).toFormat("yyyy-MM-dd");
                      const dateEnd = dt.plus({ days: 7 }).toFormat("yyyy-MM-dd");
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
