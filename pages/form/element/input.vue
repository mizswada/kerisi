<script setup>
definePageMeta({
  title: "Input",
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
      <template #header>
        <div class="flex">
          <Icon class="mr-2 flex justify-center" name="ic:outline-info"></Icon
          >Info
        </div>
      </template>
      <template #body>
        <p class="mb-4">
          This template uses
          <a
            href="https://formkit.com"
            target="_blank"
            class="hover:underline text-primary"
            >Formkit</a
          >
          which is a powerful form builder for Vue. It is a Vue component that
          allows you to create forms with a lot of different elements.
        </p>
      </template>
    </rs-card>

    <rs-card>
      <template #header>Basic Input</template>
      <template #body>
        <div class="grid grid-cols-1 md:grid-cols-3 gap-x-5">
          <FormKit type="text" label="Basic" />
          <FormKit
            type="text"
            label="Placeholder"
            placeholder="This is a placeholder"
          />
          <FormKit type="text" label="Helper Text" help="This is a help text" />
          <FormKit type="text" label="Disabled Input" disabled />
          <FormKit
            type="text"
            label="Readonly Input"
            value="Readonly"
            readonly
          />
        </div>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('basic')"
          >
            {{ showCode.basic ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.basic" class="relative" v-highlight>
              <button
                @click="copyCode('codeBasic')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeBasic']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeBasic"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeBasic" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;!-- Basic Input --&gt;
                      &lt;FormKit type="text" label="Basic"/&gt;

                      &lt;!-- Placeholder Input --&gt;
                      &lt;FormKit type="text" label="Placeholder" placeholder="This is a placeholder"/&gt;

                      &lt;!-- Helper Input --&gt;
                      &lt;FormKit type="text" label="Helper Text" help="This is a help text"/&gt;

                      &lt;!-- Disabled Input --&gt;
                      &lt;FormKit type="text" label="Disabled Text" disabled/&gt;

                      &lt;!-- Readonly Input --&gt;
                      &lt;FormKit type="text" label="Readonly Text" value="Readonly" readonly/&gt;
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
      <template #header>Type</template>
      <template #body>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-x-5">
          <FormKit type="text" label="Basic" />
          <FormKit
            type="text"
            label="Without Border"
            :classes="{
              input: '!border-solid border-b',
              inner: 'border-none',
            }"
          />
        </div>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('type')"
          >
            {{ showCode.type ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.type" class="relative" v-highlight>
              <button
                @click="copyCode('codeType')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeType']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeType"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeType" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;!-- Basic Input --&gt;
                      &lt;FormKit type="text" label="Basic"/&gt;

                      &lt;!-- Without Border Input --&gt;
                      &lt;FormKit 
                        type="text" 
                        label="Without Border"
                        :classes="{
                          input: '!border-solid border-b',
                          inner: 'border-none',
                        }"
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
      <template #header>Validation</template>
      <template #body>
        <p class="mb-4 text-justify">
          Formkit uses easy validation prop <code>validation</code> to validate
          its form element. You can use the following validation types:
          <code>required</code>, <code>email</code>, <code>url</code>,
          <code>number</code>, <code>minLength</code>, <code>maxLength</code>,
          <code>pattern</code> and others. This validation type can also be
          combined using <code>|</code>. Visit the
          <a
            href="https://formkit.com/essentials/validation"
            target="_blank"
            class="hover:underline text-primary"
            >Formkit documentation</a
          >
          for more information.
        </p>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-x-4">
          <FormKit
            type="text"
            label="Required"
            validation="required"
            validation-visibility="dirty"
          >
            <template #label>
              <label
                class="formkit-label text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm formkit-invalid:text-red-500"
                >Required <span class="text-danger">*</span></label
              >
            </template>
          </FormKit>
          <FormKit
            type="text"
            label="Email"
            validation="email"
            validation-visibility="dirty"
          >
          </FormKit>
          <FormKit
            type="text"
            label="Url"
            validation="url"
            validation-visibility="dirty"
          >
          </FormKit>
          <FormKit
            type="text"
            label="Number between 0-100"
            validation="number|between:0,100"
            validation-visibility="dirty"
          >
          </FormKit>
        </div>
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
                      &lt;!-- Required Validation with Required Symbol --&gt;
                      &lt;FormKit 
                        type="text" 
                        label="Required Validation"
                        validation="required"
                        validation-visibility="dirty"
                      &gt;
                        &lt;template #label&gt;
                          &lt;label
                            class="formkit-label text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm formkit-invalid:text-red-500"
                          &gt;
                            Required &lt;span class="text-danger"&gt;*&lt;/span&gt;
                          &lt;/label&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;

                      &lt;!-- Email Validation --&gt;
                      &lt;FormKit 
                        type="text" 
                        label="Email Validation"
                        validation="email"
                        validation-visibility="dirty"
                      /&gt;

                      &lt;!-- Url Validation --&gt;
                      &lt;FormKit 
                        type="text" 
                        label="Url Validation"
                        validation="url"
                        validation-visibility="dirty"
                      /&gt;

                      &lt;!-- Number 0-100 Validation --&gt;
                      &lt;FormKit 
                        type="text" 
                        label="Number Validation"
                        validation="number|between:0,100"
                        validation-visibility="dirty"
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
