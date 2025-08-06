<script setup>
definePageMeta({
  title: "Modal",
});

const showModal = ref({
  modal1: false,
  modal2: false,
  modal3: false,
  modal4: false,
  modal5: false,
  modal6: false,
  modal7: false,
  modal8: false,
  modal9: false,
  modal10: false,
  modal11: false,
  modal12: false,
  modal13: false,
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

const clickOK = () => {
  alert("You have clicked OK");
};

const clickCancel = () => {
  alert("You have clicked Cancel");
};
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header>Default </template>
      <template #body>
        <p class="mb-4">Use <code>rs-model</code> to show/hide modal.</p>
        <div class="flex flex-wrap items-center justify-start gap-x-6">
          <rs-button @click="showModal.modal1 = true">Show Modal</rs-button>
          <rs-button @click="showModal.modal9 = true">Hide Overlay</rs-button>
        </div>
        <rs-modal title="This is a modal" v-model="showModal.modal1">
          This is the content of modal.
        </rs-modal>
        <rs-modal
          title="This is a modal"
          v-model="showModal.modal9"
          hide-overlay
        >
          This is the content of modal.
        </rs-modal>
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
                      &lt;rs-button @click="showModal = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal"  v-model="showModal"&gt;
                        This is the content of small modal.
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModalHideOverlay = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal"  v-model="showModalHideOverlay" hide-overlay&gt;
                        This is the content of small modal.
                      &lt;/rs-modal&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt;
                    const showModal = ref(false);
                    const showModalHideOverlay = ref(false); 
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
      <template #header>Size </template>
      <template #body>
        <p class="mb-4">
          Modal size uses props <code>size</code> to change the size of modal.
          There are 3 sizes: <code>sm</code>, <code>md</code> and
          <code>lg</code>. The default size is <code>md</code>.
        </p>
        <div class="flex flex-wrap items-center justify-start gap-x-6">
          <rs-button @click="showModal.modal2 = true">Small Size</rs-button>
          <rs-button @click="showModal.modal3 = true">Medium Size</rs-button>
          <rs-button @click="showModal.modal4 = true">Large Size</rs-button>
        </div>
        <rs-modal title="This is a modal" size="sm" v-model="showModal.modal2">
          This is the content of small modal.
        </rs-modal>
        <rs-modal title="This is a modal" size="md" v-model="showModal.modal3">
          This is the content of medium modal.
        </rs-modal>
        <rs-modal title="This is a modal" size="lg" v-model="showModal.modal4">
          This is the content of large modal.
        </rs-modal>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('size')"
          >
            {{ showCode.size ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.size" class="relative" v-highlight>
              <button
                @click="copyCode('codeSize')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeSize']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeSize"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeSize" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;rs-button @click="showModalSmall = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal" size="sm"  v-model="showModalSmall"&gt;
                        This is the content of small modal.
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModalMedium = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal" size="md"  v-model="showModalMedium"&gt;
                        This is the content of medium modal.
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModaLarge = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal" size="lg"  v-model="showModaLarge"&gt;
                        This is the content of large modal.
                      &lt;/rs-modal&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt; 
                    const showModalSmall = ref(false);
                    const showModalMedium = ref(false);
                    const showModaLarge = ref(false);
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
      <template #header>Position </template>
      <template #body>
        <p class="mb-4">
          Modal position uses props <code>position</code> to change the position
          of modal. There are 3 positions: <code>top</code>,
          <code>center</code> and <code>bottom</code>. The default position is
          <code>top</code>.
        </p>
        <div class="flex flex-wrap items-center justify-start gap-x-6">
          <rs-button @click="showModal.modal5 = true">Top</rs-button>
          <rs-button @click="showModal.modal6 = true">Center</rs-button>
          <rs-button @click="showModal.modal7 = true">Bottom</rs-button>
        </div>
        <rs-modal
          title="This is a modal"
          position="top"
          v-model="showModal.modal5"
        >
          This is the content of modal.
        </rs-modal>
        <rs-modal
          title="This is a modal"
          position="center"
          v-model="showModal.modal6"
        >
          This is the content of modal.
        </rs-modal>
        <rs-modal
          title="This is a modal"
          position="bottom"
          v-model="showModal.modal7"
        >
          This is the content of modal.
        </rs-modal>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('position')"
          >
            {{ showCode.position ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.position" class="relative" v-highlight>
              <button
                @click="copyCode('codePosition')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codePosition']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codePosition"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codePosition" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;rs-button @click="showModalTop = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal" position="top" v-model="showModalTop"&gt;
                        This is the content of small modal.
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModalCenter = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal" position="center" v-model="showModalCenter"&gt;
                        This is the content of medium modal.
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModalBottom = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal" position="bottom" v-model="showModalBottom"&gt;
                        This is the content of large modal.
                      &lt;/rs-modal&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt; 
                    const showModalTop = ref(false);
                    const showModalCenter = ref(false);
                    const showModalBottom = ref(false);
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
      <template #header>Customize</template>
      <template #body>
        <p class="mb-4 text-justify">
          Modal header, body and footer can be customized by using template
          slots. The header slot named <code>header</code> and the body slot
          named <code>body</code> and the footer slot named <code>footer</code>.
          Ok title can be customized by using <code>ok-title</code> prop. The
          default title is <code>Ok</code>. Cancel title can be customized by
          using <code>cancel-title</code> prop. The default title is
          <code>Cancel</code>. Ok and cancel button can be hide by using
          <code>ok-only</code> or <code>cancel-only</code> prop. The default
          value is <code>false</code> for both.
        </p>
        <div class="flex flex-wrap items-center justify-start gap-x-6">
          <rs-button @click="showModal.modal8 = true">Show Modal</rs-button>
          <rs-button @click="showModal.modal10 = true">Custom Button</rs-button>
          <rs-button @click="showModal.modal11 = true">OK Only</rs-button>
          <rs-button @click="showModal.modal12 = true">Cancel Only</rs-button>
        </div>
        <rs-modal title="This is a modal" v-model="showModal.modal8">
          <template #header> Custom Header </template>
          <template #body>Custom Body </template>
          <template #footer> Custom Footer</template>
        </rs-modal>
        <rs-modal
          title="This is a modal"
          v-model="showModal.modal10"
          cancel-title="Close"
          ok-title="Accept"
        >
          This is the content of modal.
        </rs-modal>
        <rs-modal title="This is a modal" v-model="showModal.modal11" ok-only>
          This is the content of modal.
        </rs-modal>
        <rs-modal
          title="This is a modal"
          v-model="showModal.modal12"
          cancel-only
        >
          This is the content of modal.
        </rs-modal>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('customize')"
          >
            {{ showCode.customize ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.customize" class="relative" v-highlight>
              <button
                @click="copyCode('codeCustomize')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeCustomize']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeCustomize"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeCustomize" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;rs-button @click="showModal = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal v-model="showModal"&gt;
                        &lt;template #header&gt; Custom Header &lt;/template&gt;
                        &lt;template #body&gt; Custom Body &lt;/template&gt;
                        &lt;template #footer&gt; Custom Footer &lt;/template&gt;
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModalCustom = true"&gt;Custom Button&lt;/rs-button&gt;
                      &lt;rs-modal v-model="showModalCustom" cancel-title="Close" ok-title="Accept"&gt;
                        This is modal content.
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModalOK = true"&gt;Ok Only&lt;/rs-button&gt;
                      &lt;rs-modal v-model="showModalOK" ok-only&gt;
                        This is modal content.
                      &lt;/rs-modal&gt;
                      &lt;rs-button @click="showModalCancel = true"&gt;Cancel Only&lt;/rs-button&gt;
                      &lt;rs-modal v-model="showModalCancel" cancel-only&gt;
                        This is modal content.
                      &lt;/rs-modal&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt; 
                    const showModal = ref(false);
                    const showModalCustom = ref(false);
                    const showModalOK = ref(false);
                    const showModalCancel = ref(false);
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
      <template #header>Bind Function </template>
      <template #body>
        <p class="mb-4">
          You can bind function for ok and cancel button inside the modal by
          using <code>ok-callback</code> and <code>cancel-callback</code> prop.
        </p>
        <div class="flex flex-wrap items-center justify-start gap-x-6">
          <rs-button @click="showModal.modal13 = true">Bind Function</rs-button>
        </div>
        <rs-modal
          title="This is a modal"
          v-model="showModal.modal13"
          :ok-callback="clickOK"
          :cancel-callback="clickCancel"
        >
          This is the content of modal.
        </rs-modal>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('bindFunction')"
          >
            {{ showCode.bindFunction ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.bindFunction" class="relative" v-highlight>
              <button
                @click="copyCode('codeBindFunction')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeBindFunction']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeBindFunction"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeBindFunction" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;rs-button @click="showModal = true"&gt;Open Modal&lt;/rs-button&gt;
                      &lt;rs-modal title="This is a modal" v-model="showModal" :ok-callback="clickOK" :cancel-callback="clickCancel"&gt;
                        This is the content of small modal.
                      &lt;/rs-modal&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt; 
                    const showModal = ref(false);
                    const clickOK = () => {
                      alert("You have clicked OK");
                    };
                
                    const clickCancel = () => {
                      alert("You have clicked Cancel");
                    };
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
