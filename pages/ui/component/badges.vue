<script setup>
definePageMeta({
  title: "Badges",
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
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header>
        <h5>Default Badges</h5>
      </template>
      <template #body>
        <p class="mb-2">Use the <code>rs-badge</code> to show badges.</p>
        <div class="grid grid-cols-3 md:grid-cols-6 gap-4 gap-y-0">
          <rs-badge variant="primary" class="mb-4"> Primary </rs-badge>
          <rs-badge variant="info" class="mb-4"> Info </rs-badge>
          <rs-badge variant="success" class="mb-4"> Success </rs-badge>
          <rs-badge variant="warning" class="mb-4"> Warning </rs-badge>
          <rs-badge variant="danger" class="mb-4"> Danger </rs-badge>
        </div>
        <div class="flex justify-end">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('defaultBadges')"
          >
            {{ showCode.defaultBadges ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.defaultBadges" class="relative" v-highlight>
              <button
                @click="copyCode('codeDefaultBadges')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDefaultBadges']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDefaultBadges"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeDefaultBadges" class="language-html shadow-none">
                  <code>
                    &lt;rs-badge variant="primary"&gt;Primary&lt;/rs-badge&gt;
                    &lt;rs-badge variant="info"&gt;Info&lt;/rs-badge&gt;
                    &lt;rs-badge variant="success"&gt;Success&lt;/rs-badge&gt;
                    &lt;rs-badge variant="warning"&gt;Warning&lt;/rs-badge&gt;
                    &lt;rs-badge variant="danger"&gt;Danger&lt;/rs-badge&gt;
                  </code>
                </pre>
              </NuxtScrollbar>
            </div>
          </transition>
        </ClientOnly>
      </template>
    </rs-card>
    <!-- Badges with icon -->
    <rs-card>
      <template #header>
        <h5>Badges With Icon</h5>
      </template>
      <template #body>
        <p class="mb-2">
          Include props <code>icon</code> to <code>rs-badge</code> to show
          badges.
        </p>
        <div class="grid grid-cols-3 md:grid-cols-6 gap-4 gap-y-0">
          <rs-badge
            variant="primary"
            icon="material-symbols:star-outline-rounded"
            class="mb-4"
          >
            Primary
          </rs-badge>
          <rs-badge
            variant="info"
            icon="material-symbols:star-outline-rounded"
            class="mb-4"
          >
            Info
          </rs-badge>
          <rs-badge
            variant="success"
            icon="material-symbols:star-outline-rounded"
            class="mb-4"
          >
            Success
          </rs-badge>
          <rs-badge
            variant="warning"
            icon="material-symbols:star-outline-rounded"
            class="mb-4"
          >
            Warning
          </rs-badge>
          <rs-badge
            variant="danger"
            icon="material-symbols:star-outline-rounded"
            class="mb-4"
          >
            Danger
          </rs-badge>
        </div>
        <div class="flex justify-end">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('badgesWithIcon')"
          >
            {{ showCode.badgesWithIcon ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.badgesWithIcon" class="relative" v-highlight>
              <button
                @click="copyCode('codeBadgesWithIcon')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeBadgesWithIcon']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeBadgesWithIcon"] }}
              </span>
              <NuxtScrollbar style="max-height: 300px">
                <pre id="codeBadgesWithIcon" class="language-html shadow-none">
                  <code>
                    &lt;rs-badge variant="primary" icon="material-symbols:star-outline-rounded"&gt;Primary&lt;/rs-badge&gt;
                    &lt;rs-badge variant="info" icon="material-symbols:star-outline-rounded"&gt;Info&lt;/rs-badge&gt;
                    &lt;rs-badge variant="success" icon="material-symbols:star-outline-rounded"&gt;Success&lt;/rs-badge&gt;
                    &lt;rs-badge variant="warning" icon="material-symbols:star-outline-rounded"&gt;Warning&lt;/rs-badge&gt;
                    &lt;rs-badge variant="danger" icon="material-symbols:star-outline-rounded"&gt;Danger&lt;/rs-badge&gt;
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
