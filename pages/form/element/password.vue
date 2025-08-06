<script setup>
definePageMeta({
  title: "Input Password",
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

const password = ref("");
const inputType = ref("password");
const score = ref(null);
const strength = ref(null);

const onScore = (payload) => {
  strength.value = payload.strength;
  score.value = payload.score;
};

const toggleType = () => {
  inputType.value = inputType.value === "password" ? "text" : "password";
};

const computedtype = computed(() => {
  return inputType.value;
});
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <rs-card>
      <template #header> Default </template>
      <template #body>
        <FormKit type="password" label="Password" />
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
              <NuxtScrollbar style="height: 200px">
                <pre id="codeDefault" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit type="password" label="Password" /&gt;
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
      <template #header> Confirmation </template>
      <template #body>
        <FormKit type="group">
          <FormKit
            type="password"
            name="password"
            label="Password"
            help="Enter a new password"
            validation="required"
          />
          <FormKit
            type="password"
            name="password_confirm"
            label="Confirm password"
            help="Confirm your new password"
            validation="required|confirm"
            validation-label="Password confirmation"
          />
        </FormKit>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('confirmation')"
          >
            {{ showCode.confirmation ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.confirmation" class="relative" v-highlight>
              <button
                @click="copyCode('codeConfirmation')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeConfirmation']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeConfirmation"] }}
              </span>
              <NuxtScrollbar style="height: 300px">
                <pre id="codeConfirmation" class="language-html shadow-none">
                  <code>
                    &lt;template&gt;
                      &lt;FormKit type="group"&gt;
                        &lt;FormKit
                          type="password"
                          name="password"
                          label="Password"
                          help="Enter a new password"
                          validation="required"
                        /&gt;
                        &lt;FormKit
                          type="password"
                          name="password_confirm"
                          label="Confirm password"
                          help="Confirm your new password"
                          validation="required|confirm"
                          validation-label="Password confirmation"
                        /&gt;
                      &lt;/FormKit&gt;
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
      <template #header> Validation </template>
      <template #body>
        <FormKit
          type="password"
          label="Validate Password"
          validation="matches:/^(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9])(?=.*?[^\w\s]).{8,}$/"
          :validation-messages="{
            matches:
              'The password must contain at least one uppercase letter, one lowercase letter, one digit, one special character and should have 8 characters long.',
          }"
          validation-visibility="dirty"
        />
        <FormKit :type="computedtype" label="See Password">
          <template #suffix>
            <button
              class="h-full rounded-r-md p-2 flex justify-center items-center"
              @click="toggleType"
            >
              <Icon
                v-if="computedtype == 'password'"
                name="mdi:eye-outline"
                size="19"
              ></Icon>
              <Icon v-else name="mdi:eye-off-outline" size="19"></Icon>
            </button>
          </template>
        </FormKit>
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
                      &lt;!-- Password Validation Uppercase, Lowercase, One digit and Special Character --&gt;
                      &lt;FormKit 
                        type="password" 
                        label="Validate Password" 
                        validation="matches:/^(?=.*?[A-Z])(?=.*?[a-z])(?=.*?[0-9])(?=.*?[^\w\s]).{8,}$/" 
                        :validation-messages="{
                          matches:
                            'The password must contain at least one uppercase letter, one lowercase letter, one digit, one special character and should have 8 characters long.',
                        }" 
                        validation-visibility="dirty" 
                      /&gt;

                      &lt;!-- Password Toggle --&gt;
                      &lt;FormKit 
                        :type="computedtype" 
                        label="See Password"
                      &gt;
                        &lt;template #suffix&gt;
                          &lt;button class="h-full rounded-r-md p-2 flex justify-center items-center" @click="toggleType"&gt;
                            &lt;Icon 
                              v-if="computedtype == 'password'" 
                              name="mdi:eye-outline" 
                              size="19"&gt;
                            &lt;/Icon&gt;
                            &lt;Icon 
                              v-else 
                              name="mdi:eye-off-outline" 
                              size="19"&gt;
                            &lt;/Icon&gt;
                          &lt;/button&gt;
                        &lt;/template&gt;
                      &lt;/FormKit&gt;
                    &lt;/template&gt;
 
                    &lt;script setup&gt;
                      const inputType = ref("password");
                      const computedtype = computed(() => {
                        return inputType.value;
                      });
                      const toggleType = () => {
                        inputType.value = inputType.value === "password" ? "text" : "password";
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
