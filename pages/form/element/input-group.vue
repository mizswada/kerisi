<script setup>
definePageMeta({
  title: "Input Group",
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

const key = ref("jg9dsk23rnfsd");

const generateKey = () => {
  key.value = Math.random().toString(36).substring(2, 15);
};

const computedKey = computed(() => {
  return key.value;
});
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header>Prefix</template>
      <template #body>
        <!-- Prefix with button text -->
        <FormKit type="text" label="Text" placeholder="1.00">
          <template #prefix> <div class="p-3">RM</div> </template>
        </FormKit>

        <!-- Prefix with button text -->
        <FormKit type="text" placeholder="1.00">
          <template #prefix>
            <div class="bg-[rgb(var(--bg-1))] h-full rounded-l-md p-3">RM</div>
          </template>
        </FormKit>

        <!-- Prefix Icon -->
        <FormKit type="text" label="Icon" placeholder="Icon">
          <template #prefix>
            <Icon class="px-2" name="ic:outline-info"></Icon>
          </template>
        </FormKit>

        <!-- Prefix with button text -->
        <FormKit
          type="text"
          label="Button"
          v-model="computedKey"
          placeholder="Button"
        >
          <template #prefix>
            <button
              class="bg-slate-100 hover:bg-slate-200 dark:bg-slate-700 hover:dark:bg-slate-900 h-full rounded-l-md p-3 flex justify-center items-center"
              @click="generateKey"
            >
              <Icon name="ic:round-refresh" size="19"></Icon>
            </button>
          </template>
        </FormKit>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('prefix')"
          >
            {{ showCode.prefix ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.prefix" class="relative" v-highlight>
              <button
                @click="copyCode('codePrefix')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codePrefix']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codePrefix"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codePrefix" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;!-- Prefix with button text --&gt;
                      &lt;FormKit type="text" label="Text" placeholder="1.00"&gt;
                        &lt;template #prefix&gt; &lt;div class="p-3"&gt;RM&lt;/div&gt; &lt;/template&gt;
                      &lt;/FormKit&gt;

                      &lt;!-- Prefix with button text --&gt;
                      &lt;FormKit type="text" placeholder="1.00"&gt;
                        &lt;template #prefix&gt;
                          &lt;div class="bg-[rgb(var(--bg-1))] h-full rounded-l-md p-3"&gt;
                            RM
                          &lt;/div&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;

                      &lt;!-- Prefix Icon --&gt;
                      &lt;FormKit type="text" label="Icon" placeholder="Icon"&gt;
                        &lt;template #prefix&gt;
                          &lt;Icon class="px-2" name="ic:outline-info"&gt;&lt;/Icon&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;

                      &lt;!-- Prefix with button text --&gt;
                      &lt;FormKit type="text" label="Button" v-model="computedKey" placeholder="Button"&gt;
                        &lt;template #prefix&gt;
                          &lt;button
                            class="bg-slate-100 hover:bg-slate-200 dark:bg-slate-700 hover:dark:bg-slate-900 h-full rounded-l-md p-3 flex justify-center items-center"
                            @click="generateKey"
                          &gt;
                            &lt;Icon name="ic:round-refresh" size="19"&gt;&lt;/Icon&gt;
                          &lt;/button&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;
                    const key = ref("jg9dsk23rnfsd");

                    const generateKey = () => {
                      key.value = Math.random().toString(36).substring(2, 15);
                    };

                    const computedKey = computed(() => {
                      return key.value;
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
      <template #header>Suffix</template>
      <template #body>
        <!-- Suffix with button text -->
        <FormKit type="text" label="Text" placeholder="10.00">
          <template #suffix> <div class="p-3">Kg</div> </template>
        </FormKit>

        <!-- Suffix with button text -->
        <FormKit type="text" placeholder="1.00">
          <template #suffix>
            <div class="bg-[rgb(var(--bg-1))] h-full rounded-r-md p-3">Kg</div>
          </template>
        </FormKit>

        <!-- Suffix Icon -->
        <FormKit type="text" label="Icon" placeholder="Icon">
          <template #suffix>
            <Icon class="px-2" name="ic:outline-info"></Icon>
          </template>
        </FormKit>

        <!-- Suffix with button text -->
        <FormKit
          type="text"
          label="Button"
          v-model="computedKey"
          placeholder="Button"
        >
          <template #suffix>
            <button
              class="bg-slate-100 hover:bg-slate-200 dark:bg-slate-700 hover:dark:bg-slate-900 h-full rounded-r-md p-3 flex justify-center items-center"
              @click="generateKey"
            >
              <Icon name="ic:round-refresh" size="19"></Icon>
            </button>
          </template>
        </FormKit>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('suffix')"
          >
            {{ showCode.suffix ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.suffix" class="relative" v-highlight>
              <button
                @click="copyCode('codeSuffix')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeSuffix']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeSuffix"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeSuffix" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;!-- Suffix with button text --&gt;
                      &lt;FormKit type="text" label="Text" placeholder="1.00"&gt;
                        &lt;template #suffix&gt; &lt;div class="p-3"&gt;RM&lt;/div&gt; &lt;/template&gt;
                      &lt;/FormKit&gt;

                      &lt;!-- Suffix with button text --&gt;
                      &lt;FormKit type="text" placeholder="1.00"&gt;
                        &lt;template #suffix&gt;
                          &lt;div class="bg-[rgb(var(--bg-1))] h-full rounded-r-md p-3"&gt;
                            RM
                          &lt;/div&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;

                      &lt;!-- Suffix Icon --&gt;
                      &lt;FormKit type="text" label="Icon" placeholder="Icon"&gt;
                        &lt;template #suffix&gt;
                          &lt;Icon class="px-2" name="ic:outline-info"&gt;&lt;/Icon&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;

                      &lt;!-- Suffix with button text --&gt;
                      &lt;FormKit type="text" label="Button" v-model="computedKey" placeholder="Button"&gt;
                        &lt;template #suffix&gt;
                          &lt;button
                            class="bg-slate-100 hover:bg-slate-200 dark:bg-slate-700 hover:dark:bg-slate-900 h-full rounded-r-md p-3 flex justify-center items-center"
                            @click="generateKey"
                          &gt;
                            &lt;Icon name="ic:round-refresh" size="19"&gt;&lt;/Icon&gt;
                          &lt;/button&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;
                    const key = ref("jg9dsk23rnfsd");

                    const generateKey = () => {
                      key.value = Math.random().toString(36).substring(2, 15);
                    };

                    const computedKey = computed(() => {
                      return key.value;
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
      <template #header>Fieldset</template>
      <template #body>
        <rs-fieldset label="Group Input">
          <FormKit type="text" label="Text 1" />
          <FormKit type="text" label="Text 2" />
        </rs-fieldset>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('fieldset')"
          >
            {{ showCode.fieldset ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.fieldset" class="relative" v-highlight>
              <button
                @click="copyCode('codeFieldset')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeFieldset']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeFieldset"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeFieldset" class="language-html shadow-none">
                  <code>
                    &lt;rs-fieldset  label="Group Input"&gt;
                        &lt;FormKit type="text" label="Text 1" /&gt;
                        &lt;FormKit type="text" label="Text 2" /&gt;
                    &lt;/rs-fieldset&gt;

                    &lt;script setup&gt;
                  
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
