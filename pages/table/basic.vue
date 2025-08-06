<script setup>
definePageMeta({
  title: "Table Basic",
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

const field = ["Id", "Name", "Amount(RM)", "Discount(%)"];
const data = [
  {
    id: 1,
    fullName: "Margit, The Fallen Omen",
    age: 25,
    email: "margit25@gmail.com",
  },
  {
    id: 2,
    fullName: "Malenia",
    age: 50,
    email: "malenia@gmail.com",
  },
  {
    id: 3,
    fullName: "Godrick Soldier",
    age: 30,
    email: "godrick@gmail.com",
  },
  {
    id: 4,
    fullName: "Godwyn, The Graften",
    age: 70,
    email: "godwyn@gmail.com",
  },
  {
    id: 5,
    fullName: "Ranni",
    age: 26,
    email: "ranni@gmail.com",
  },
];
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
          This is a basic table template. It is a Vue component that allows you
          to create tables with a lot of different elements.
        </p>
      </template>
    </rs-card>

    <rs-card>
      <template #header> Basic </template>
      <template #body>
        <rs-table
          :field="field"
          :data="data"
          :options="{
            variant: 'default',
            striped: true,
            borderless: true,
            hover: true,
            fixed: false,
          }"
          basic
        >
        </rs-table>
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
                      &lt;rs-table
                        :field="field"
                        :data="data"
                        :options="{
                          variant: 'default',
                          striped: true,
                          borderless: true,
                          hover: true,
                          fixed: false,
                        }"
                        basic
                      &gt;
                      &lt;/rs-table&gt;
                    &lt;/template&gt;
   
                    &lt;script setup&gt;
                      const field = ["Id", "Name", "Amount(RM)", "Discount(%)"];
                      const data = [
                        {
                          id: 1,
                          fullName: "Margit, The Fallen Omen",
                          age: 25,
                          email: "margit25@gmail.com",
                        },
                        {
                          id: 2,
                          fullName: "Malenia",
                          age: 50,
                          email: "malenia@gmail.com",
                        },
                        {
                          id: 3,
                          fullName: "Godrick Soldier",
                          age: 30,
                          email: "godrick@gmail.com",
                        },
                        {
                          id: 4,
                          fullName: "Godwyn, The Graften",
                          age: 70,
                          email: "godwyn@gmail.com",
                        },
                        {
                          id: 5,
                          fullName: "Ranni",
                          age: 26,
                          email: "ranni@gmail.com",
                        },
                      ];
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
