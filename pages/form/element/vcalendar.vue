<script setup>
import { DateTime } from "luxon";

definePageMeta({
  title: "VCalendar",
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

const date = ref(DateTime.now());
const range = ref({
  start: DateTime.local(2022, 6, 6),
  end: DateTime.local(2022, 6, 20),
});
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header>
        <div class="flex">
          <Icon class="mr-2 flex justify-center" name="ic:outline-info"></Icon
          >Info
        </div>
      </template>
      <template #body>
        <p class="mb-4">
          This template use
          <a
            href="https://vcalendar.io/"
            target="_blank"
            class="hover:underline text-primary"
            >V-Calendar</a
          >
          which is a Vue component that allows you to create a calendar.
        </p>
      </template>
    </rs-card>
    <rs-card>
      <template #header> Single Date </template>
      <template #body>
        <p class="mb-4">
          Single date calendar is a calendar that only allows you to select a
          single date. It is a good way to select a date for a datepicker.
        </p>
        <div class="flex flex-col justify-center items-center">
          <v-date-picker v-model="date" />

          <p class="py-2">
            <span class="font-semibold">Date:</span>
            {{ date }}
          </p>
        </div>
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
                      &lt;div class="flex flex-col justify-center items-center"&gt;
                        &lt;v-date-picker v-model="date" /&gt;
                      &lt;/div&gt;
                    &lt;/template&gt;
      
                    &lt;script setup&gt;
                      import { DateTime } from "luxon";
      
                      const date = ref(DateTime.now());
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
      <template #header> Date Range </template>
      <template #body>
        <p class="mb-4">
          Date range calendar is a calendar that allows you to select a date
          range.
        </p>
        <div class="flex flex-col justify-center items-center">
          <v-date-picker v-model="range" is-range />
          <p class="py-2">
            <span class="font-semibold">Date:</span>
            {{ range }}
          </p>
        </div>
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
              <NuxtScrollbar style="height: 400px">
                <pre id="codeRange" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;div class="flex flex-col justify-center items-center"&gt;
                        &lt;v-date-picker v-model="range" is-range /&gt;
                      &lt;/div&gt;
                    &lt;/template&gt;
      
                    &lt;script setup&gt;
                      import { DateTime } from "luxon";
      
                      const range = ref({
                        start: DateTime.local(2022, 6, 6),
                        end: DateTime.local(2022, 6, 20),
                      });
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
      <template #header> Date & Time </template>
      <template #body>
        <p class="mb-4">
          Date & time calendar is a calendar that allows you to select a date
          and time.
        </p>
        <div class="flex flex-col justify-center items-center">
          <v-date-picker v-model="date" mode="dateTime" />
          <p class="py-2">
            <span class="font-semibold">Date:</span>
            {{ date }}
          </p>
        </div>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('dateTime')"
          >
            {{ showCode.dateTime ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.dateTime" class="relative" v-highlight>
              <button
                @click="copyCode('codeDateTime')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDateTime']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDateTime"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeDateTime" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;div class="flex flex-col justify-center items-center"&gt;
                        &lt;v-date-picker v-model="date" mode="dateTime" /&gt;
                      &lt;/div&gt;
                    &lt;/template&gt;
      
                    &lt;script setup&gt;
                      import { DateTime } from "luxon";
      
                      const date = ref(DateTime.now());
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
