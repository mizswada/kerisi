<script setup>
definePageMeta({
  title: "Input Mask",
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
            href="https://beholdr.github.io/maska/#/"
            target="_blank"
            class="hover:underline text-primary"
            >Maska</a
          >
          which is a powerful input mask for Vue. It is a Vue component that
          allows you to create forms with a lot of different elements.
        </p>
      </template>
    </rs-card>
    <rs-card>
      <template #header> Mask </template>
      <template #body>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-x-5">
          <FormKit
            type="mask"
            label="Phone with 012-34567890"
            mask="###-########"
          />
          <FormKit type="mask" label="Date with MM/DD/YYYY" mask="##/##/####" />
          <FormKit
            type="mask"
            label="Credit / Debit Card"
            mask="#### #### #### ####"
          />
          <FormKit
            type="mask"
            label="Identity Card (IC)"
            mask="######-##-####"
          />
        </div>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('mask')"
          >
            {{ showCode.mask ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.mask" class="relative" v-highlight>
              <button
                @click="copyCode('codeMask')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeMask']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeMask"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeMask" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;!-- Phone with 012-34567890 --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="Phone with 012-34567890"
                        mask="###-########"
                      /&gt;

                      &lt;!-- Date with MM/DD/YYYY --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="Date with MM/DD/YYYY"
                        mask="##/##/####"
                      /&gt;

                      &lt;!-- Credit / Debit Card --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="Credit / Debit Card"
                        mask="#### #### #### ####"
                      /&gt;

                      &lt;!-- Identity Card (IC) --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="Identity Card (IC)"
                        mask="######-##-####"
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
      <template #header> Advanced </template>
      <template #body>
        <div class="grid grid-cols-1 md:grid-cols-2 gap-x-5">
          <!-- Mask Only A-Z -->
          <FormKit
            type="mask"
            label="Mask Letter A-F"
            mask="@@@@@@@"
            :maskOptions="{ tokens: '@:[a-fA-F]' }"
          />

          <!-- Mask Only 0-9 -->
          <FormKit
            type="mask"
            label="Mask Digit 1-5"
            mask="########"
            :maskOptions="{ tokens: '#:[1-5]' }"
          />

          <!-- Mask Only 0-9 and A-Z -->
          <FormKit
            type="mask"
            label="Mask 4 Letter A-Z and 6 Digit 0-9"
            mask="@@@@@ ######"
            :maskOptions="{ tokens: '@:[a-zA-Z]|#:[0-9]' }"
          />

          <FormKit
            type="mask"
            label="ATM Cash Withdrawal"
            mask="#,###,###.##"
            :maskOptions="{ reversed: true, eager: true }"
          />
        </div>
        <div class="flex justify-end mt-4">
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
                      &lt;!-- Mask Only A-Z --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="Mask Letter A-F"
                        mask="@@@@@@@"
                        :maskOptions="{ tokens: '@:[a-fA-F]' }"
                      /&gt;

                      &lt;!-- Mask Only 0-9 --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="Mask Digit 1-5"
                        mask="########"
                        :maskOptions="{ tokens: '#:[1-5]' }"
                      /&gt;

                      &lt;!-- Mask Only 0-9 and A-Z --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="Mask 4 Letter A-Z and 6 Digit 0-9"
                        mask="@@@@@ ######"
                        :maskOptions="{ tokens: '@:[a-zA-Z]|#:[0-9]' }"
                      /&gt;

                      &lt;!-- ATM Cash Withdrawal --&gt;
                      &lt;FormKit 
                        type="mask" 
                        label="ATM Cash Withdrawal"
                        mask="#,###,###.##"
                        :maskOptions="{ reversed: true , eager: true }"
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
