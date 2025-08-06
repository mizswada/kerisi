<script setup>
definePageMeta({
  title: "Tab",
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

const vertical = ref(false);
const type = ref("default");
const variant = ref("primary");
const fill = ref(false);
const justify = ref("left");

const variantOptions = [
  { label: "Primary", value: "primary" },
  { label: "Secondary", value: "secondary" },
  { label: "Success", value: "success" },
  { label: "Danger", value: "danger" },
  { label: "Warning", value: "warning" },
  { label: "Info", value: "info" },
];
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header> Default </template>
      <template #body>
        <p class="mb-4">
          Use the <code>rs-tab</code> component to create a tabbed interface.
          <code>rs-tab-item</code> is used to create each tab. The tabs are
          rendered as a list of links. The selected tab is styled differently to
          indicate it is active.
        </p>
        <rs-tab>
          <rs-tab-item title="Tab 1"> Tab 1 </rs-tab-item>
          <rs-tab-item title="Tab 2"> Tab 2 </rs-tab-item>
          <rs-tab-item title="Tab 3"> Tab 3 </rs-tab-item>
        </rs-tab>
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
                      &lt;rs-tab&gt;
                        &lt;rs-tab-item title="Tab 1"&gt; Tab 1 &lt;/rs-tab-item&gt;
                        &lt;rs-tab-item title="Tab 2"&gt; Tab 2 &lt;/rs-tab-item&gt;
                        &lt;rs-tab-item title="Tab 3"&gt; Tab 3 &lt;/rs-tab-item&gt;
                      &lt;/rs-tab&gt;
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
      <template #header> Vertical Tab </template>
      <template #body>
        <p class="mb-4">
          Use props <code>vertical</code> to create a vertical tabbed interface.
        </p>
        <rs-tab vertical>
          <rs-tab-item title="Tab 1"> Tab 1 </rs-tab-item>
          <rs-tab-item title="Tab 2"> Tab 2 </rs-tab-item>
          <rs-tab-item title="Tab 3"> Tab 3 </rs-tab-item>
        </rs-tab>

        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('vertical')"
          >
            {{ showCode.vertical ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.vertical" class="relative" v-highlight>
              <button
                @click="copyCode('codeVertical')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeVertical']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeVertical"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeVertical" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;rs-tab vertical&gt;
                        &lt;rs-tab-item title="Tab 1"&gt; Tab 1 &lt;/rs-tab-item&gt;
                        &lt;rs-tab-item title="Tab 2"&gt; Tab 2 &lt;/rs-tab-item&gt;
                        &lt;rs-tab-item title="Tab 3"&gt; Tab 3 &lt;/rs-tab-item&gt;
                      &lt;/rs-tab&gt;
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
      <template #header> Styling </template>
      <template #body>
        <p class="mb-4">
          The <code>rs-tab</code> can be styled using the following props
          <code>variant</code> and <code>type</code>. The <code>type</code> prop
          can be used to create a card and border style tab. The
          <code>variant</code> prop can be used to change the color of the tab.
        </p>
        <div class="flex flex-wrap gap-x-5">
          <FormKit
            label="Variant"
            type="radio"
            v-model="variant"
            :options="variantOptions"
            :classes="{
              options: 'flex flex-wrap gap-4 pt-2',
              fieldset: 'px-4 py-0 max-w-full',
            }"
          />

          <FormKit
            v-model="type"
            type="radio"
            label="Type"
            :options="[
              { label: 'Default', value: 'default' },
              { label: 'Border', value: 'border' },
              { label: 'Card', value: 'card' },
            ]"
            :classes="{
              options: 'flex flex-wrap gap-4 pt-2',
              fieldset: 'px-4 py-0 max-w-full',
            }"
          />
          <FormKit
            v-model="vertical"
            type="radio"
            label="Vertical"
            :options="[
              { label: 'False', value: false },
              { label: 'True', value: true },
            ]"
            :classes="{
              options: 'flex flex-wrap gap-4 pt-2',
              fieldset: 'px-4 py-0 max-w-full',
            }"
          />
          <FormKit
            v-model="fill"
            type="radio"
            label="Fill"
            :options="[
              { label: 'False', value: false },
              { label: 'True', value: true },
            ]"
            :classes="{
              options: 'flex flex-wrap gap-4 pt-2',
              fieldset: 'px-4 py-0 max-w-full',
            }"
          />
          <FormKit
            v-model="justify"
            type="radio"
            label="Justify"
            :options="[
              { label: 'Left', value: 'left' },
              { label: 'Center', value: 'center' },
              { label: 'Right', value: 'right' },
            ]"
            :classes="{
              options: 'flex flex-wrap gap-4 pt-2',
              fieldset: 'px-4 py-0 max-w-full',
            }"
          />
        </div>

        <rs-tab
          :variant="variant"
          :type="type"
          :fill="fill"
          :justify="justify"
          :vertical="vertical"
        >
          <rs-tab-item title="Tab 1"> Tab 1 </rs-tab-item>
          <rs-tab-item title="Tab 2"> Tab 2 </rs-tab-item>
          <rs-tab-item title="Tab 3"> Tab 3 </rs-tab-item>
        </rs-tab>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('styling')"
          >
            {{ showCode.styling ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.styling" class="relative" v-highlight>
              <button
                @click="copyCode('codeStyling')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeStyling']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeStyling"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeStyling" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;rs-tab 
                          variant="primary|secondary|info|success|warning|danger" 
                          type="default|border|card"
                          justify="left|center|right"
                          fill
                          vertical&gt;
                        &lt;rs-tab-item title="Tab 1"&gt; Tab 1 &lt;/rs-tab-item&gt;
                        &lt;rs-tab-item title="Tab 2"&gt; Tab 2 &lt;/rs-tab-item&gt;
                        &lt;rs-tab-item title="Tab 3"&gt; Tab 3 &lt;/rs-tab-item&gt;
                      &lt;/rs-tab&gt;
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
