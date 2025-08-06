<script setup>
definePageMeta({
  title: "Card",
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
        <h5>Default Card</h5>
      </template>
      <template #body>
        <p class="mb-2">
          Use the <code>rs-card</code> component to create a card layout.
        </p>
        <rs-card class="mb-4">
          <template #header>
            <h6>Card Header</h6>
          </template>
          <template #body>
            <p>This is the main content of the card.</p>
          </template>
          <template #footer>
            <p>Card Footer</p>
          </template>
        </rs-card>
        <div class="flex justify-end">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('defaultCard')"
          >
            {{ showCode.defaultCard ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.defaultCard" class="relative" v-highlight>
              <button
                @click="copyCode('codeDefaultCard')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDefaultCard']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDefaultCard"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeDefaultCard" class="language-html shadow-none">
                  <code>
                    &lt;rs-card&gt;
                      &lt;template #header&gt;
                        &lt;h6&gt;Card Header&lt;/h6&gt;
                      &lt;/template&gt;
                      &lt;template #body&gt;
                        &lt;p&gt;This is the main content of the card.&lt;/p&gt;
                      &lt;/template&gt;
                      &lt;template #footer&gt;
                        &lt;p&gt;Card Footer&lt;/p&gt;
                      &lt;/template&gt;
                    &lt;/rs-card&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <rs-card>
      <template #header>
        <h5>Card with Main Slot</h5>
      </template>
      <template #body>
        <p class="mb-2">
          You can use the default slot to add content directly to the card
          without using the body slot.
        </p>
        <rs-card class="mb-4">
          <p>This content is added using the default slot.</p>
        </rs-card>
        <div class="flex justify-end">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('mainSlotCard')"
          >
            {{ showCode.mainSlotCard ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.mainSlotCard" class="relative" v-highlight>
              <button
                @click="copyCode('codeMainSlotCard')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeMainSlotCard']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeMainSlotCard"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeMainSlotCard" class="language-html shadow-none">
                  <code>
                    &lt;rs-card&gt;
                      &lt;p&gt;This content is added using the default slot.&lt;/p&gt;
                    &lt;/rs-card&gt;
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

<style scoped lang="scss">
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
