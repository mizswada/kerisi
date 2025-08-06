<script setup>
definePageMeta({
  title: "Alert",
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
        console.log("Kod disalin ke papan klip");
        showTooltip(codeId, "Kod disalin!");
      })
      .catch((err) => {
        console.error("Gagal menyalin kod: ", err);
        showTooltip(codeId, "Gagal menyalin kod");
      });
  }
};

const showTooltip = (codeId, message) => {
  tooltips[codeId] = message;
  setTimeout(() => {
    tooltips[codeId] = null;
  }, 2000);
};

const autoDismiss = ref(false);
const timer = ref(1000);

const resetAlert = () => {
  autoDismiss.value = true;
};
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header>
        <h5>Default Alert</h5>
      </template>
      <template #body>
        <p class="mb-2">Use the <code>rs-alert</code> to show alert.</p>
        <rs-alert variant="primary" class="mb-4"
          >Hi, this is a primary alert!</rs-alert
        >
        <rs-alert variant="info" class="mb-4"
          >Hi, this is an info alert!</rs-alert
        >
        <rs-alert variant="success" class="mb-4"
          >Hi, this is a success alert!</rs-alert
        >
        <rs-alert variant="warning" class="mb-4"
          >Hi, this is a warning alert!</rs-alert
        >
        <rs-alert variant="danger" class="mb-4"
          >Hi, this is a danger alert!</rs-alert
        >
        <div class="flex justify-end">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('defaultAlert')"
          >
            {{ showCode.defaultAlert ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.defaultAlert" class="relative" v-highlight>
              <button
                @click="copyCode('codeDefaultAlert')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDefaultAlert']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDefaultAlert"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeDefaultAlert" class="language-html shadow-none">
                  <code>
                    &lt;rs-alert variant="primary"&gt;Hi, this is a primary alert!&lt;/rs-alert&gt;
                    &lt;rs-alert variant="info"&gt;Hi, this is an info alert!&lt;/rs-alert&gt;
                    &lt;rs-alert variant="success"&gt;Hi, this is a success alert!&lt;/rs-alert&gt;
                    &lt;rs-alert variant="warning"&gt;Hi, this is a warning alert!&lt;/rs-alert&gt;
                    &lt;rs-alert variant="danger"&gt;Hi, this is a danger alert!&lt;/rs-alert&gt;
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
        <h5>Auto Dismissal</h5>
      </template>
      <template #body>
        <p class="mb-2">
          Use the <code>auto-dismiss</code> prop to auto dismiss the alert. The
          default timer is 1000ms. You can change the timer by passing a value
          to the <code>timer</code> prop.
        </p>
        <rs-alert v-if="!autoDismiss" variant="primary" class="mb-4"
          >Hi, this is not auto dismiss alert!</rs-alert
        >
        <rs-alert
          v-else
          variant="primary"
          class="mb-4"
          auto-dismiss
          :timer="timer"
          >Hi, this is auto dismiss alert!</rs-alert
        >
        <div class="flex items-center gap-x-2">
          <FormKit
            type="number"
            name="timer"
            value="1000"
            step="100"
            v-model="timer"
            :classes="{
              outer: 'mb-0',
              inner: '!mb-0',
            }"
          />
          <rs-button @click="resetAlert"> Set Timer</rs-button>
        </div>

        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('autoDismiss')"
          >
            {{ showCode.autoDismiss ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.autoDismiss" class="relative" v-highlight>
              <button
                @click="copyCode('codeAutoDismiss')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeAutoDismiss']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeAutoDismiss"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeAutoDismiss" class="language-html shadow-none">
                  <code>
                    &lt;rs-alert auto-dismiss :timer="1000"&gt;Hi, this is an auto dismissable alert!&lt;/rs-alert&gt;
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
