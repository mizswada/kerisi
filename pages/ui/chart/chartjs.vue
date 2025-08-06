<script setup>
import {
  BarChart,
  useBarChart,
  DoughnutChart,
  useDoughnutChart,
  LineChart,
  useLineChart,
  PieChart,
  usePieChart,
  PolarAreaChart,
  usePolarAreaChart,
  RadarChart,
  useRadarChart,
  BubbleChart,
  useBubbleChart,
  ScatterChart,
  useScatterChart,
} from "vue-chart-3";

definePageMeta({
  title: "Chart JS",
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

const data = ref([30, 40, 60, 70, 5]);
const data2 = ref([
  { x: 20, y: 25, r: 22 },
  { x: 10, y: 23, r: 5 },
  { x: 30, y: 40, r: 25 },
  { x: 40, y: 20, r: 10 },
  { x: 15, y: 5, r: 10 },
  { x: 17, y: 10, r: 5 },
]);

const options = computed(() => ({
  scales: {
    y: {
      beginAtZero: true,
    },
  },
  plugins: {
    zoom: {
      zoom: {
        wheel: {
          enabled: true,
        },
        pinch: {
          enabled: true,
        },
        mode: "xy",
      },
    },
    legend: {
      display: true,
    },
  },
}));

const optionsHide = computed(() => ({
  scales: {
    y: {
      beginAtZero: true,
      display: false,
    },
    x: {
      display: false,
    },
  },
  plugins: {
    zoom: {
      zoom: {
        wheel: {
          enabled: true,
        },
        pinch: {
          enabled: true,
        },
        mode: "xy",
      },
    },
    legend: {
      display: true,
    },
  },
}));

const chartData = computed(() => ({
  labels: [
    "Customer 1",
    "Customer 2",
    "Customer 3",
    "Customer 4",
    "Customer 5",
  ],
  datasets: [
    {
      label: "Order",
      data: data.value,
      backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
    },
  ],
}));

const chartDataArr = computed(() => ({
  datasets: [
    {
      label: "First Dataset",
      data: data2.value,
      backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
    },
  ],
}));

const { barChartProps } = useBarChart({
  chartData,
  options,
});

const { doughnutChartProps } = useDoughnutChart({
  chartData,
  options: optionsHide,
});

const { lineChartProps } = useLineChart({
  chartData,
  options,
});

const { pieChartProps } = usePieChart({
  chartData,
  options: optionsHide,
});

const { polarAreaChartProps } = usePolarAreaChart({
  chartData,
  options: optionsHide,
});

const { radarChartProps } = useRadarChart({
  chartData,
  options: optionsHide,
});

const { bubbleChartProps } = useBubbleChart({
  chartData: chartDataArr,
  options: options,
});

const { scatterChartProps } = useScatterChart({
  chartData: chartDataArr,
  options: options,
});
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <p class="mb-4">
      We ensure Chartjs is fully supported using vue 3. Read the full
      documentation
      <a
        href="https://vue-chart-3.netlify.app/"
        class="text-blue-500 hover:underline"
        target="_blank"
        >here</a
      >.
    </p>
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
      <rs-card>
        <template #header>
          <h5>Pie Chart</h5>
        </template>
        <template #body>
          <PieChart
            v-bind="pieChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('pie')"
            >
              {{ showCode.pie ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.pie" class="relative" v-highlight>
                <button
                  @click="copyCode('codePie')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codePie']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codePie"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codePie" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;PieChart
                            v-bind="pieChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { PieChart, usePieChart } from "vue-chart-3";
                      
                      const data = ref([30, 40, 60, 70, 5]);
                      const chartData = computed(() => ({
                        labels: [
                          "Customer 1",
                          "Customer 2",
                          "Customer 3",
                          "Customer 4",
                          "Customer 5",
                        ],
                        datasets: [
                          {
                            label: "Order",
                            data: data.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                            display: false,
                          },
                          x: {
                            display: false,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { pieChartProps } = usePieChart({
                        chartData,
                        options: chartOptions,
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

      <rs-card>
        <template #header>
          <h5>Doughnut Chart</h5>
        </template>
        <template #body>
          <DoughnutChart
            v-bind="doughnutChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('doughnut')"
            >
              {{ showCode.doughnut ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.doughnut" class="relative" v-highlight>
                <button
                  @click="copyCode('codeDoughnut')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeDoughnut']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeDoughnut"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeDoughnut" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;DoughnutChart
                            v-bind="doughnutChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { DoughnutChart, useDoughnutChart } from "vue-chart-3";
                      
                      const data = ref([30, 40, 60, 70, 5]);
                      const chartData = computed(() => ({
                        labels: [
                          "Customer 1",
                          "Customer 2",
                          "Customer 3",
                          "Customer 4",
                          "Customer 5",
                        ],
                        datasets: [
                          {
                            label: "Order",
                            data: data.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                            display: false,
                          },
                          x: {
                            display: false,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { doughnutChartProps } = useDoughnutChart({
                        chartData,
                        options: chartOptions,
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

      <rs-card>
        <template #header>
          <h5>Line Chart</h5>
        </template>
        <template #body>
          <LineChart
            v-bind="lineChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('line')"
            >
              {{ showCode.line ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.line" class="relative" v-highlight>
                <button
                  @click="copyCode('codeLine')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeLine']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeLine"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeLine" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;LineChart
                            v-bind="lineChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { LineChart, useLineChart } from "vue-chart-3";
                      
                      const data = ref([30, 40, 60, 70, 5]);
                      const chartData = computed(() => ({
                        labels: [
                          "Customer 1",
                          "Customer 2",
                          "Customer 3",
                          "Customer 4",
                          "Customer 5",
                        ],
                        datasets: [
                          {
                            label: "Order",
                            data: data.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { lineChartProps } = useLineChart({
                        chartData,
                        options: chartOptions,
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

      <rs-card>
        <template #header>
          <h5>Bar Chart</h5>
        </template>
        <template #body>
          <BarChart
            v-bind="barChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('bar')"
            >
              {{ showCode.bar ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.bar" class="relative" v-highlight>
                <button
                  @click="copyCode('codeBar')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeBar']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeBar"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeBar" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;BarChart
                            v-bind="barChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { BarChart, useBarChart } from "vue-chart-3";
                      
                      const data = ref([30, 40, 60, 70, 5]);
                      const chartData = computed(() => ({
                        labels: [
                          "Customer 1",
                          "Customer 2",
                          "Customer 3",
                          "Customer 4",
                          "Customer 5",
                        ],
                        datasets: [
                          {
                            label: "Order",
                            data: data.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { barChartProps } = useBarChart({
                        chartData,
                        options: chartOptions,
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

      <rs-card>
        <template #header>
          <h5>Polar Area Chart</h5>
        </template>
        <template #body>
          <PolarAreaChart
            v-bind="polarAreaChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('polarArea')"
            >
              {{ showCode.polarArea ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.polarArea" class="relative" v-highlight>
                <button
                  @click="copyCode('codePolarArea')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codePolarArea']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codePolarArea"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codePolarArea" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;PolarAreaChart
                            v-bind="polarAreaChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { PolarAreaChart, usePolarAreaChart } from "vue-chart-3";
                      
                      const data = ref([30, 40, 60, 70, 5]);
                      const chartData = computed(() => ({
                        labels: [
                          "Customer 1",
                          "Customer 2",
                          "Customer 3",
                          "Customer 4",
                          "Customer 5",
                        ],
                        datasets: [
                          {
                            label: "Order",
                            data: data.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                            display: false,
                          },
                          x: {
                            display: false,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { polarAreaChartProps } = usePolarAreaChart({
                        chartData,
                        options: chartOptions,
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

      <rs-card>
        <template #header>
          <h5>Radar Chart</h5>
        </template>
        <template #body>
          <RadarChart
            v-bind="radarChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('radar')"
            >
              {{ showCode.radar ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.radar" class="relative" v-highlight>
                <button
                  @click="copyCode('codeRadar')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeRadar']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeRadar"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeRadar" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;RadarChart
                            v-bind="radarChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { RadarChart, useRadarChart } from "vue-chart-3";
                      
                      const data = ref([30, 40, 60, 70, 5]);
                      const chartData = computed(() => ({
                        labels: [
                          "Customer 1",
                          "Customer 2",
                          "Customer 3",
                          "Customer 4",
                          "Customer 5",
                        ],
                        datasets: [
                          {
                            label: "Order",
                            data: data.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                            display: false,
                          },
                          x: {
                            display: false,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { radarChartProps } = useRadarChart({
                        chartData,
                        options: chartOptions,
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

      <rs-card class="col-span-2">
        <template #header>
          <h5>Bubble Chart</h5>
        </template>
        <template #body>
          <BubbleChart
            v-bind="bubbleChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('bubble')"
            >
              {{ showCode.bubble ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.bubble" class="relative" v-highlight>
                <button
                  @click="copyCode('codeBubble')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeBubble']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeBubble"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeBubble" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;BubbleChart
                            v-bind="bubbleChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { BubbleChart, useBubbleChart } from "vue-chart-3";
                      
                      const data2 = ref([
                        { x: 20, y: 25, r: 22 },
                        { x: 10, y: 23, r: 5 },
                        { x: 30, y: 40, r: 25 },
                        { x: 40, y: 20, r: 10 },
                        { x: 15, y: 5, r: 10 },
                        { x: 17, y: 10, r: 5 },
                      ]);
                      const chartData = computed(() => ({
                        datasets: [
                          {
                            label: "First Dataset",
                            data: data2.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { bubbleChartProps } = useBubbleChart({
                        chartData,
                        options: chartOptions,
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

      <rs-card class="col-span-2">
        <template #header>
          <h5>Scatter Chart</h5>
        </template>
        <template #body>
          <ScatterChart
            v-bind="scatterChartProps"
            style="position: relative; height: 40vh; width: 80vw"
          />
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('scatter')"
            >
              {{ showCode.scatter ? "Hide Code" : "Show Code" }}
            </button>
            <ClientOnly>
              <transition name="fade">
                <div v-show="showCode.scatter" class="relative" v-highlight>
                  <button
                    @click="copyCode('codeScatter')"
                    class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                  >
                    Copy
                  </button>
                  <span
                    v-if="tooltips['codeScatter']"
                    class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                  >
                    {{ tooltips["codeScatter"] }}
                  </span>
                  <NuxtScrollbar style="height: 400px">
                    <pre id="codeScatter" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                          &lt;ScatterChart
                            v-bind="scatterChartProps"
                            style="position: relative; height: 40vh; width: 80vw"
                          /&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      import { ScatterChart, useScatterChart } from "vue-chart-3";
                      
                      const data2 = ref([
                        { x: 20, y: 25, r: 22 },
                        { x: 10, y: 23, r: 5 },
                        { x: 30, y: 40, r: 25 },
                        { x: 40, y: 20, r: 10 },
                        { x: 15, y: 5, r: 10 },
                        { x: 17, y: 10, r: 5 },
                      ]);
                      const chartData = computed(() => ({
                        datasets: [
                          {
                            label: "First Dataset",
                            data: data2.value,
                            backgroundColor: ["#FF829D", "#FFD778", "#5EB5EF", "#6FCDCD", "#ECEDF1"],
                          },
                        ],
                      }));

                      const chartOptions = computed(() => ({
                        scales: {
                          y: {
                            beginAtZero: true,
                          },
                        },
                        plugins: {
                          zoom: {
                            zoom: {
                              wheel: {
                                enabled: true,
                              },
                              pinch: {
                                enabled: true,
                              },
                              mode: "xy",
                            },
                          },
                          legend: {
                            display: true,
                          },
                        },
                      }));

                      const { scatterChartProps } = useScatterChart({
                        chartData,
                        options: chartOptions,
                      });

                      &lt;/script&gt;
                    </code>
                  </pre>
                  </NuxtScrollbar>
                </div>
              </transition>
            </ClientOnly>
          </div>
        </template>
      </rs-card>
    </div>
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
