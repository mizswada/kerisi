<script setup>
definePageMeta({
  title: "Collapse",
});

const type = ref("default");
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
      <template #header> Default </template>
      <template #body>
        <p class="mb-2">
          Use the <code>rs-collapse</code> to show collapse.
          <code>rs-collapse-item</code> is used to show collapse item.
        </p>
        <rs-collapse>
          <rs-collapse-item title="Collapse Item 1">
            <p class="text-justify">
              Lorem Ipsum is simply dummy text of the printing and typesetting
              industry. Lorem Ipsum has been the industry's standard dummy text
              ever since the 1500s, when an unknown printer took a galley of
              type and scrambled it to make a type specimen book. It has
              survived not only five centuries, but also the leap into
              electronic typesetting, remaining essentially unchanged. It was
              popularised in the 1960s with the release of Letraset sheets
              containing Lorem Ipsum passages, and more recently with desktop
              publishing software like Aldus PageMaker including versions of
              Lorem Ipsum.
            </p>
          </rs-collapse-item>
          <rs-collapse-item title="Collapse Item 2">
            <p>Collapse Item 1 content</p>
          </rs-collapse-item>
        </rs-collapse>
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
                    &lt;rs-collapse&gt;
                      &lt;rs-collapse-item title="Collapse Item 1"&gt;Collapse Item 1 content&lt;/rs-collapse-item&gt;
                      &lt;rs-collapse-item title="Collapse Item 2"&gt;Collapse Item 2 content&lt;/rs-collapse-item&gt;
                    &lt;/rs-collapse&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <rs-card>
      <template #header> Accordion </template>
      <template #body>
        <p class="mb-2">
          Use the
          <code>accordion</code> prop to <code>rs-collapse</code> to make it
          accordion.
        </p>
        <rs-collapse accordion>
          <rs-collapse-item title="Accordion Item 1">
            <p class="text-justify">
              Lorem Ipsum is simply dummy text of the printing and typesetting
              industry. Lorem Ipsum has been the industry's standard dummy text
              ever since the 1500s, when an unknown printer took a galley of
              type and scrambled it to make a type specimen book. It has
              survived not only five centuries, but also the leap into
              electronic typesetting, remaining essentially unchanged. It was
              popularised in the 1960s with the release of Letraset sheets
              containing Lorem Ipsum passages, and more recently with desktop
              publishing software like Aldus PageMaker including versions of
              Lorem Ipsum.
            </p>
          </rs-collapse-item>
          <rs-collapse-item title="Accordion Item 2">
            <p>Collapse Item 1 content</p>
          </rs-collapse-item>
        </rs-collapse>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('accordion')"
          >
            {{ showCode.accordion ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.accordion" class="relative" v-highlight>
              <button
                @click="copyCode('codeAccordion')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeAccordion']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeAccordion"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeAccordion" class="language-html shadow-none">
                  <code>
                    &lt;rs-collapse accordion&gt;
                      &lt;rs-collapse-item title="Accordion Item 1"&gt;Accordion Item 1 content&lt;/rs-collapse-item&gt;
                      &lt;rs-collapse-item title="Accordion Item 2"&gt;Accordion Item 2 content&lt;/rs-collapse-item&gt;
                    &lt;/rs-collapse&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>

    <rs-card>
      <template #header> Type </template>
      <template #body>
        <p class="mb-2">
          Use the
          <code>type</code> prop to <code>rs-collapse</code> to make it
          accordion. The type can be <code>default</code>,
          <code>border</code> and <code>card</code>.
        </p>
        <FormKit
          v-model="type"
          type="radio"
          label="Type"
          :options="[
            { label: 'Default', value: 'default' },
            { label: 'Border', value: 'border' },
            { label: 'Card', value: 'card' },
          ]"
        />
        <rs-collapse :type="type">
          <rs-collapse-item title="Collapse Item 1">
            <p class="text-justify">
              Lorem Ipsum is simply dummy text of the printing and typesetting
              industry. Lorem Ipsum has been the industry's standard dummy text
              ever since the 1500s, when an unknown printer took a galley of
              type and scrambled it to make a type specimen book. It has
              survived not only five centuries, but also the leap into
              electronic typesetting, remaining essentially unchanged. It was
              popularised in the 1960s with the release of Letraset sheets
              containing Lorem Ipsum passages, and more recently with desktop
              publishing software like Aldus PageMaker including versions of
              Lorem Ipsum.
            </p>
          </rs-collapse-item>
          <rs-collapse-item title="Collapse Item 2">
            <p>Collapse Item 2 content</p>
          </rs-collapse-item>
          <rs-collapse-item title="Collapse Item 3">
            <p>Collapse Item 3 content</p>
          </rs-collapse-item>
        </rs-collapse>
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
                    &lt;!-- Default Styling('default') --&gt;
                    &lt;rs-collapse&gt;
                      &lt;rs-collapse-item title="Collapse Item 1"&gt;Collapse Item 1 content&lt;/rs-collapse-item&gt;
                      &lt;rs-collapse-item title="Collapse Item 2"&gt;Collapse Item 2 content&lt;/rs-collapse-item&gt;
                    &lt;/rs-collapse&gt;

                    &lt;!-- Border Styling('border') --&gt;
                    &lt;rs-collapse type="border"&gt;
                      &lt;rs-collapse-item title="Collapse Item 1"&gt;Collapse Item 1 content&lt;/rs-collapse-item&gt;
                      &lt;rs-collapse-item title="Collapse Item 2"&gt;Collapse Item 2 content&lt;/rs-collapse-item&gt;
                    &lt;/rs-collapse&gt;

                    &lt;!-- Card Styling('card') --&gt;
                    &lt;rs-collapse type="card"&gt;
                      &lt;rs-collapse-item title="Collapse Item 1"&gt;Collapse Item 1 content&lt;/rs-collapse-item&gt;
                      &lt;rs-collapse-item title="Collapse Item 2"&gt;Collapse Item 2 content&lt;/rs-collapse-item&gt;
                    &lt;/rs-collapse&gt;
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
