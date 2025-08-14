<script setup>
import { useLayoutStore } from "~/stores/layout";
import RSHeader from "~/components/layouts/Header.vue";
import RSSideMenu from "~~/components/layouts/sidemenu/index.vue";
import RsFloatingChat from "~/components/RsFloatingChat.vue";
import { useWindowSize } from "vue-window-size";

const { width } = useWindowSize();
const layoutStore = useLayoutStore();
const mobileWidth = layoutStore.mobileWidth;

// watch for window size changes
watch(
  () => [width.value],
  ([width]) => {
    if (width <= mobileWidth) {
      document.querySelector(".h-layout").classList.add("menu-hide");
      document.getElementsByClassName("menu-overlay")[0].classList.add("hide");
    } else document.querySelector(".h-layout").classList.remove("menu-hide");
  }
);

function toggleMenu(event) {
  document.querySelector(".h-layout").classList.toggle("menu-hide");
  document.getElementsByClassName("menu-overlay")[0].classList.toggle("hide");
}

// Floating chat functionality
const showFloatingChat = ref(true);
const chatBadge = ref(null);

const handleChatClick = () => {
  // Handle chat click - you can open a modal, navigate to chat page, etc.
  console.log('Floating chat clicked!');
  // Example: Open chat modal or navigate to chat page
  // navigateTo('/chat');
};

// Example: Set badge count (you can fetch this from API)
const setChatBadge = (count) => {
  chatBadge.value = count;
};
</script>

<template>
  <RSHeader @toggleMenu="toggleMenu" />
  <RSSideMenu />
  <div class="content-page duration-300">
    <slot />
  </div>
  <div @click="toggleMenu" class="menu-overlay"></div>
  
  <!-- Floating Chat -->
  <RsFloatingChat
    :show="showFloatingChat"
    :badge="chatBadge"
    badge-color="danger"
    position="bottom-right"
    size="xl"
    tooltip="Chat with Krisi"
    :use-image="true"
    image="@/assets/img/avatar_krisi.png"
    image-alt="Krisi Avatar"
    @click="handleChatClick"
  />
</template>

