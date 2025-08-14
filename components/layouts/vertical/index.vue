<script setup>
import { useLayoutStore } from "~/stores/layout";

import RSHeader from "~/components/layouts/Header.vue";
import RSSideMenu from "~~/components/layouts/sidemenu/index.vue";
import RsFloatingChat from "~/components/RsFloatingChat.vue";
import RsChatModal from "~/components/RsChatModal.vue";
// import RSConfigMenu from "~~/components/layouts/configmenu/index.vue";
// import RSFooter from "~/components/layouts/Footer.vue";
import { useWindowSize } from "vue-window-size";

const { width } = useWindowSize();
const layoutStore = useLayoutStore();
const mobileWidth = layoutStore.mobileWidth;

// watch for window size changes
watch(
  () => [width.value],
  ([width]) => {
    if (width <= mobileWidth) {
      document.querySelector(".v-layout").classList.add("menu-hide");
      document.getElementsByClassName("menu-overlay")[0].classList.add("hide");
    } else document.querySelector(".v-layout").classList.remove("menu-hide");
  }
);

function toggleMenu(event) {
  document.querySelector(".v-layout").classList.toggle("menu-hide");
  document.getElementsByClassName("menu-overlay")[0].classList.toggle("hide");
}

// Floating chat functionality
const showFloatingChat = ref(true);
const chatBadge = ref(null);
const showChatModal = ref(false);

const handleChatClick = () => {
  showChatModal.value = true;
};

const handleSendMessage = (message) => {
  // Handle sending message - you can integrate with your API here
  console.log('Sending message:', message);
  
  // Example: You can add API call here to send message to backend
  // await $fetch('/api/chat', {
  //   method: 'POST',
  //   body: { message }
  // });
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
  <!-- <RSConfigMenu /> -->
  <div @click="toggleMenu" class="menu-overlay"></div>

  <!-- <RSFooter /> -->
  
  <!-- Floating Chat -->
  <RsFloatingChat
    :show="showFloatingChat && !showChatModal"
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

  <!-- Chat Modal -->
  <RsChatModal
    v-model="showChatModal"
    position="bottom-right"
    size="md"
    title="KRISI Assistant"
    assistant-name="KRISI Assistant"
    @send-message="handleSendMessage"
  />
</template>
