<script setup>
definePageMeta({
  title: "VueQuill",
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
      <template #header> Info </template>
      <template #body>
        <p class="text-justify mb-4">
          <a
            href="https://vueup.github.io/vue-quill/"
            target="_blank"
            class="hover:underline text-primary"
            >VueQuill</a
          >
          is a WYSIWYG editor that can be used to create content in a visual
          manner. It is a fully-featured editor that can be used to create
          content in a visual manner. VueQuill is a Component for building rich
          text editors, powered by Vue 3 and Quill.
        </p>
      </template>
    </rs-card>

    <rs-card>
      <template #header> How to use? </template>
      <template #body>
        <p class="mb-4">
          There are editor configuration options that can be used to customize
          the editor. For example, you can set the editor language, the editor
          theme, the editor toolbar, the editor plugins, etc. The editor
          configuration options are available in the editor configuration
          object. see this link for more information:
          <a
            href="https://vueup.github.io/vue-quill/guide/"
            target="_blank"
            class="hover:underline text-primary"
            >https://vueup.github.io/vue-quill/guide/</a
          >
        </p>

        <div class="mb-4">
          <label
            for="country"
            class="text-gray-700 dark:text-gray-200 block mb-2 font-semibold text-sm"
            >Advanced List</label
          >
          <ClientOnly>
            <QuillEditor theme="snow"> </QuillEditor>
          </ClientOnly>
        </div>
        <div class="flex justify-end">
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
              <NuxtScrollbar style="height: 250px">
                <pre id="codeDefault" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;QuillEditor theme="snow"&gt; &lt;/QuillEditor&gt;
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
