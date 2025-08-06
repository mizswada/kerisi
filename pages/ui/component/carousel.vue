<script setup>
import { Navigation, Pagination, Scrollbar, A11y } from "swiper";

definePageMeta({
  title: "Carousel",
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

const onSwiper = (swiper) => {
  // console.log(swiper);
};
const onSlideChange = () => {
  // console.log("slide change");
};

const modules = [Navigation, Pagination, Scrollbar, A11y];
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
          This template use
          <a
            href="https://swiperjs.com/vue"
            target="_blank"
            class="hover:underline text-primary"
            >Swiper JS</a
          >
          which is a Vue component that allows you to create a carousel.
        </p>
      </template>
    </rs-card>
    <rs-card>
      <template #header> Default (Horizontal)</template>
      <template #body>
        <swiper
          :modules="modules"
          :slides-per-view="1"
          :centeredSlides="true"
          :pagination="{ clickable: true }"
          @swiper="onSwiper"
          @slideChange="onSlideChange"
          navigation
        >
          <swiper-slide v-for="n in 10" :key="n">
            <div class="w-full flex justify-center items-center">
              <img
                :src="'https://picsum.photos/1024/600?random=' + n"
                class="object-contain w-full md:w-1/2"
              />
            </div>
          </swiper-slide>
        </swiper>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('defaultCarousel')"
          >
            {{ showCode.defaultCarousel ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.defaultCarousel" class="relative" v-highlight>
              <button
                @click="copyCode('codeDefaultCarousel')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDefaultCarousel']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDefaultCarousel"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeDefaultCarousel" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;swiper
                        :modules="modules"
                        :slides-per-view="1"
                        :centeredSlides="true"
                        :pagination="{ clickable: true }"
                        @swiper="onSwiper"
                        @slideChange="onSlideChange"
                        navigation
                      &gt;
                        &lt;swiper-slide v-for="n in 10" :key="n"&gt;
                          &lt;div class="w-full flex justify-center items-center"&gt;
                            &lt;img
                              :src="'https://picsum.photos/1024/600?random=' + n"
                              class="object-contain w-full md:w-1/2"
                            /&gt;
                          &lt;/div&gt;
                        &lt;/swiper-slide&gt;
                      &lt;/swiper&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt; 
                    import { Navigation, Pagination, Scrollbar, A11y } from "swiper";

                    const onSwiper = (swiper) => {
                      // console.log(swiper);
                    };
                    const onSlideChange = () => {
                      // console.log("slide change");
                    };

                    const modules = [Navigation, Pagination, Scrollbar, A11y];
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
