<script setup>
definePageMeta({
  title: "Apex Chart",
});

const changeKey = ref(0);

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

const series = ref([
  {
    name: "Series 1",
    data: [30, 40, 35, 50, 49, 60, 70, 91],
  },
  {
    name: "Series 2",
    data: [20, 10, 25, 40, 39, 50, 60, 71],
  },
  {
    name: "Series 3",
    data: [0, 10, 20, 25, 30, 35, 40, 45],
  },
]);

const series2 = ref([
  {
    data: [{ x: "Category 1", y: [30, 40, 35, 50, 49, 60, 70, 91] }],
  },
  {
    data: [{ x: "Category 2", y: [20, 10, 25, 40, 39, 50, 60, 71] }],
  },
  {
    data: [{ x: "Category 3", y: [0, 10, 20, 25, 30, 35, 40, 45] }],
  },
]);

const series3 = ref([30, 40, 35, 50, 49, 60, 70, 91]);

const series4 = ref([77]);

const chartOptions = computed(() => ({
  chart: {
    id: "apexChart",
  },
  legend: {
    position: "top",
  },
  theme: {
    mode: "light",
    palette: "palette1",
  },
  xaxis: {
    categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
  },
  responsive: [
    {
      breakpoint: 768,
      options: {
        legend: {
          position: "bottom",
        },
      },
    },
  ],
}));

const chartOptionsRadial = computed(() => ({
  colors: ["#20E647"],
  plotOptions: {
    radialBar: {
      hollow: {
        margin: 0,
        size: "70%",
        background: "#293450",
      },
      track: {
        dropShadow: {
          enabled: true,
          top: 2,
          left: 0,
          blur: 4,
          opacity: 0.15,
        },
      },
      dataLabels: {
        name: {
          offsetY: -10,
          color: "#fff",
          fontSize: "13px",
        },
        value: {
          color: "#fff",
          fontSize: "30px",
          show: true,
        },
      },
    },
  },
  fill: {
    type: "gradient",
    gradient: {
      shade: "dark",
      type: "vertical",
      gradientToColors: ["#87D4F9"],
      stops: [0, 100],
    },
  },
  stroke: {
    lineCap: "round",
  },
  labels: ["Goals"],
}));

onMounted(() => {
  setTimeout(() => {
    changeKey.value++;
  }, 500);
});
</script>

<template>
  <div>
    <LayoutsBreadcrumb />
    <p class="mb-4">
      We ensure Apex Chart is fully supported using vue 3. Read the full
      documentation
      <a
        href="https://apexcharts.com/docs/vue-charts/"
        target="_blank"
        class="text-blue-500 hover:text-blue-600"
        >here</a
      >.
    </p>
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-4">
      <rs-card>
        <template #header>
          <h5>Bar Chart</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="300"
              type="bar"
              :options="chartOptions"
              :series="series"
            ></VueApexCharts>
          </ClientOnly>
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
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="300"
                            type="bar"
                            :options="chartOptions"
                            :series="series"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series = ref([
                        {
                          name: "Series 1",
                          data: [30, 40, 35, 50, 49, 60, 70, 91],
                        },
                        {
                          name: "Series 2",
                          data: [20, 10, 25, 40, 39, 50, 60, 71],
                        },
                        {
                          name: "Series 3",
                          data: [0, 10, 20, 25, 30, 35, 40, 45],
                        },
                      ]);

                      const chartOptions = computed(() => ({
                        chart: {
                          id: "apexChart",
                        },
                        legend: {
                          position: "top",
                        },
                        theme: {
                          mode: "light",
                          palette: "palette1",
                        },
                        xaxis: {
                          categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
                        },
                        responsive: [
                          {
                            breakpoint: 768,
                            options: {
                              legend: {
                                position: "bottom",
                              },
                            },
                          },
                        ],
                      }));

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

      <rs-card>
        <template #header>
          <h5>Box Plot Chart</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="300"
              type="boxPlot"
              :options="chartOptions"
              :series="series2"
            ></VueApexCharts>
          </ClientOnly>
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('boxPlot')"
            >
              {{ showCode.boxPlot ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.boxPlot" class="relative" v-highlight>
                <button
                  @click="copyCode('codeBoxPlot')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeBoxPlot']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeBoxPlot"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeBoxPlot" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="300"
                            type="boxPlot"
                            :options="chartOptions"
                            :series="series2"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series2 = ref([
                        {
                          data: [{ x: "Category 1", y: [30, 40, 35, 50, 49, 60, 70, 91] }],
                        },
                        {
                          data: [{ x: "Category 2", y: [20, 10, 25, 40, 39, 50, 60, 71] }],
                        },
                        {
                          data: [{ x: "Category 3", y: [0, 10, 20, 25, 30, 35, 40, 45] }],
                        },
                      ]);

                      const chartOptions = computed(() => ({
                        chart: {
                          id: "apexChart",
                        },
                        legend: {
                          position: "top",
                        },
                        theme: {
                          mode: "light",
                          palette: "palette1",
                        },
                        xaxis: {
                          categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
                        },
                        responsive: [
                          {
                            breakpoint: 768,
                            options: {
                              legend: {
                                position: "bottom",
                              },
                            },
                          },
                        ],
                      }));

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

      <rs-card>
        <template #header>
          <h5>Line Chart</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="300"
              type="line"
              :options="chartOptions"
              :series="series"
            ></VueApexCharts>
          </ClientOnly>
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
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="300"
                            type="line"
                            :options="chartOptions"
                            :series="series"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series = ref([
                        {
                          name: "Series 1",
                          data: [30, 40, 35, 50, 49, 60, 70, 91],
                        },
                        {
                          name: "Series 2",
                          data: [20, 10, 25, 40, 39, 50, 60, 71],
                        },
                        {
                          name: "Series 3",
                          data: [0, 10, 20, 25, 30, 35, 40, 45],
                        },
                      ]);

                      const chartOptions = computed(() => ({
                        chart: {
                          id: "apexChart",
                        },
                        legend: {
                          position: "top",
                        },
                        theme: {
                          mode: "light",
                          palette: "palette1",
                        },
                        xaxis: {
                          categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
                        },
                        responsive: [
                          {
                            breakpoint: 768,
                            options: {
                              legend: {
                                position: "bottom",
                              },
                            },
                          },
                        ],
                      }));

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

      <rs-card>
        <template #header>
          <h5>Donut Chart</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="300"
              type="donut"
              :options="chartOptions"
              :series="series3"
            ></VueApexCharts>
          </ClientOnly>
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('donut')"
            >
              {{ showCode.donut ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.donut" class="relative" v-highlight>
                <button
                  @click="copyCode('codeDonut')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeDonut']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeDonut"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeDonut" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="300"
                            type="donut"
                            :options="chartOptions"
                            :series="series3"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series3 = ref([30, 40, 35, 50, 49, 60, 70, 91]);

                      const chartOptions = computed(() => ({
                        chart: {
                          id: "apexChart",
                        },
                        legend: {
                          position: "top",
                        },
                        theme: {
                          mode: "light",
                          palette: "palette1",
                        },
                        responsive: [
                          {
                            breakpoint: 768,
                            options: {
                              legend: {
                                position: "bottom",
                              },
                            },
                          },
                        ],
                      }));

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

      <rs-card>
        <template #header>
          <h5>Pie Chart</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="300"
              type="pie"
              :options="chartOptions"
              :series="series3"
            ></VueApexCharts>
          </ClientOnly>
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
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="300"
                            type="pie"
                            :options="chartOptions"
                            :series="series3"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series3 = ref([30, 40, 35, 50, 49, 60, 70, 91]);

                      const chartOptions = computed(() => ({
                        chart: {
                          id: "apexChart",
                        },
                        legend: {
                          position: "top",
                        },
                        theme: {
                          mode: "light",
                          palette: "palette1",
                        },
                        responsive: [
                          {
                            breakpoint: 768,
                            options: {
                              legend: {
                                position: "bottom",
                              },
                            },
                          },
                        ],
                      }));

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

      <rs-card>
        <template #header>
          <h5>Area Chart</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="300"
              type="area"
              :options="chartOptions"
              :series="series"
            ></VueApexCharts>
          </ClientOnly>
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('area')"
            >
              {{ showCode.area ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.area" class="relative" v-highlight>
                <button
                  @click="copyCode('codeArea')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeArea']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeArea"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeArea" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="300"
                            type="area"
                            :options="chartOptions"
                            :series="series"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series = ref([
                        {
                          name: "Series 1",
                          data: [30, 40, 35, 50, 49, 60, 70, 91],
                        },
                        {
                          name: "Series 2",
                          data: [20, 10, 25, 40, 39, 50, 60, 71],
                        },
                        {
                          name: "Series 3",
                          data: [0, 10, 20, 25, 30, 35, 40, 45],
                        },
                      ]);

                      const chartOptions = computed(() => ({
                        chart: {
                          id: "apexChart",
                        },
                        legend: {
                          position: "top",
                        },
                        theme: {
                          mode: "light",
                          palette: "palette1",
                        },
                        xaxis: {
                          categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
                        },
                        responsive: [
                          {
                            breakpoint: 768,
                            options: {
                              legend: {
                                position: "bottom",
                              },
                            },
                          },
                        ],
                      }));

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

      <rs-card>
        <template #header>
          <h5>Radar Chart</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="300"
              type="radar"
              :options="chartOptions"
              :series="series"
            ></VueApexCharts>
          </ClientOnly>
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
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="300"
                            type="radar"
                            :options="chartOptions"
                            :series="series"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series = ref([
                        {
                          name: "Series 1",
                          data: [30, 40, 35, 50, 49, 60, 70, 91],
                        },
                        {
                          name: "Series 2",
                          data: [20, 10, 25, 40, 39, 50, 60, 71],
                        },
                        {
                          name: "Series 3",
                          data: [0, 10, 20, 25, 30, 35, 40, 45],
                        },
                      ]);

                      const chartOptions = computed(() => ({
                        chart: {
                          id: "apexChart",
                        },
                        legend: {
                          position: "top",
                        },
                        theme: {
                          mode: "light",
                          palette: "palette1",
                        },
                        xaxis: {
                          categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
                        },
                        responsive: [
                          {
                            breakpoint: 768,
                            options: {
                              legend: {
                                position: "bottom",
                              },
                            },
                          },
                        ],
                      }));

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

      <rs-card>
        <template #header>
          <h5>Radial Bar</h5>
        </template>
        <template #body>
          <ClientOnly>
            <VueApexCharts
              :key="changeKey"
              width="100%"
              height="330"
              type="radialBar"
              :options="chartOptionsRadial"
              :series="series4"
            ></VueApexCharts>
          </ClientOnly>
          <div class="flex justify-end mt-4">
            <button
              class="text-sm border border-slate-200 py-1 px-3 rounded-lg"
              @click="toggleCode('radialBar')"
            >
              {{ showCode.radialBar ? "Hide Code" : "Show Code" }}
            </button>
          </div>
          <ClientOnly>
            <transition name="fade">
              <div v-show="showCode.radialBar" class="relative" v-highlight>
                <button
                  @click="copyCode('codeRadialBar')"
                  class="absolute top-4 right-2 text-sm bg-gray-300 hover:bg-gray-400 py-1 px-3 rounded z-10"
                >
                  Copy
                </button>
                <span
                  v-if="tooltips['codeRadialBar']"
                  class="absolute top-4 right-20 bg-black text-white text-xs rounded py-1 px-2 z-20"
                >
                  {{ tooltips["codeRadialBar"] }}
                </span>
                <NuxtScrollbar style="height: 400px">
                  <pre id="codeRadialBar" class="language-html shadow-none">
                    <code>
                      &lt;template&gt; 
                        &lt;ClientOnly&gt;
                          &lt;VueApexCharts
                            :key="changeKey"
                            width="100%"
                            height="330"
                            type="radialBar"
                            :options="chartOptionsRadial"
                            :series="series4"
                          &gt;&lt;/VueApexCharts&gt;
                        &lt;/ClientOnly&gt;
                      &lt;/template&gt;

                      &lt;script setup&gt;
                      const changeKey = ref(0);
                      
                      const series4 = ref([77]);

                      const chartOptionsRadial = computed(() => ({
                        colors: ["#20E647"],
                        plotOptions: {
                          radialBar: {
                            hollow: {
                              margin: 0,
                              size: "70%",
                              background: "#293450",
                            },
                            track: {
                              dropShadow: {
                                enabled: true,
                                top: 2,
                                left: 0,
                                blur: 4,
                                opacity: 0.15,
                              },
                            },
                            dataLabels: {
                              name: {
                                offsetY: -10,
                                color: "#fff",
                                fontSize: "13px",
                              },
                              value: {
                                color: "#fff",
                                fontSize: "30px",
                                show: true,
                              },
                            },
                          },
                        },
                        fill: {
                          type: "gradient",
                          gradient: {
                            shade: "dark",
                            type: "vertical",
                            gradientToColors: ["#87D4F9"],
                            stops: [0, 100],
                          },
                        },
                        stroke: {
                          lineCap: "round",
                        },
                        labels: ["Goals"],
                      }));

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
