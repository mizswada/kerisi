<script setup>
import { DateTime } from "luxon";

const { $FullCalendar } = useNuxtApp();
const FullCalendar = $FullCalendar;
definePageMeta({
  title: "Calendar",
});

const calendarOptions = ref(null);

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

const handleDateClick = (arg) => {
  // alert("date click! " + arg.dateStr);
};

// get current date YYYY-MM-DD
const startDate = DateTime.now().toISODate();
const endDate = DateTime.now().plus({ days: 4 }).toISODate();

// get date plus 3 days
const datePlus3 = DateTime.now().plus({ days: 6 }).toISODate();

console.log("startDate", startDate);
console.log("endDate", endDate);
console.log("datePlus3", datePlus3);

const events = [
  {
    title: "Event",
    startDate: "2022-07-01",
    endDate: "2022-07-06",
    color: "green",
  },
];

calendarOptions.value = {
  ...FullCalendar?.options,
  initialView: "dayGridMonth",
  headerToolbar: {
    left: "prev,next title",
    right: "dayGridMonth,timeGridWeek,listWeek",
  },
  dateClick: handleDateClick,
  dragScroll: true,
  dayMaxEvents: 2,
  navLinks: true,
  events: [
    { title: "event 1", start: startDate, end: endDate },
    { title: "event 2", start: startDate, end: endDate },
    { title: "event 3", start: startDate, end: endDate },
    { title: "event 3", start: startDate, end: endDate },
    { title: "event 3", start: startDate, end: endDate },
    { title: "event 3", start: startDate, end: endDate },
    { title: "event 4", date: datePlus3 },
  ],
};

const changeKey = ref(0);

onMounted(() => {
  setTimeout(() => {
    changeKey.value++;
  }, 500);
});
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
          This template uses
          <a
            href="https://fullcalendar.io/"
            target="_blank"
            class="hover:underline text-primary"
            >FullCalendar</a
          >
          which is a Vue component that allows you to create a calendar.
          FullCalendar is a JavaScript library that provides a simple API for
          easily creating a calendar. Besides, it is a great way to display
          events on a page.
        </p>
      </template>
    </rs-card>
    <rs-card>
      <template #header> Default </template>
      <template #body>
        <p class="mb-4">
          Use <code>rs-calendar</code> to display a calendar. The calendar is a
          simple component that displays a grid of days.
        </p>
        <ClientOnly>
          <FullCalendar :key="changeKey" :options="calendarOptions" />
        </ClientOnly>
        <div class="flex justify-end mt-4">
          <button
            class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
            @click="toggleCode('defaultCalendar')"
          >
            {{ showCode.defaultCalendar ? "Hide Code" : "Show Code" }}
          </button>
        </div>
        <ClientOnly>
          <transition name="fade">
            <div v-show="showCode.defaultCalendar" class="relative" v-highlight>
              <button
                @click="copyCode('codeDefaultCalendar')"
                class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
              >
                Copy
              </button>
              <span
                v-if="tooltips['codeDefaultCalendar']"
                class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
              >
                {{ tooltips["codeDefaultCalendar"] }}
              </span>
              <NuxtScrollbar style="height: 400px">
                <pre id="codeDefaultCalendar" class="language-html shadow-none">
                  <code>
                    &lt;template&gt; 
                      &lt;ClientOnly&gt;
                        &lt;FullCalendar :key="changeKey" :options="calendarOptions" /&gt;
                      &lt;/ClientOnly&gt;
                    &lt;/template&gt;

                    &lt;script setup&gt; 
                    const { $FullCalendar } = useNuxtApp();
                    const FullCalendar = $FullCalendar;

                    const calendarOptions = ref(null);

                    calendarOptions.value = {
                      ...FullCalendar?.options,
                      initialView: "dayGridMonth",
                      headerToolbar: {
                        left: "prev,next title",
                        right: "dayGridMonth,timeGridWeek,listWeek",
                      },
                      dragScroll: true,
                      dayMaxEvents: 2,
                      navLinks: true,
                      events: [
                        { title: "event 1", start: "2022-07-05", end: "2022-07-08" },
                        { title: "event 2", start: "2022-07-05", end: "2022-07-08" },
                        { title: "event 3", start: "2022-07-05", end: "2022-07-08" },
                        { title: "event 3", start: "2022-07-05", end: "2022-07-08" },
                        { title: "event 3", start: "2022-07-05", end: "2022-07-08" },
                        { title: "event 3", start: "2022-07-05", end: "2022-07-08" },
                        { title: "event 4", date: "2022-07-22" },
                      ],
                    };

                    const changeKey = ref(0);

                    onMounted(() => {
                      setTimeout(() => {
                        changeKey.value++;
                      }, 500);
                    });
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
