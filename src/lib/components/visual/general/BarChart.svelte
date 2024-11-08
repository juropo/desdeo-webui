<!-- @component
Renders a single horizontal bar chart using the ECharts library.

Displays a stacked horizontal bar chart with configurable data,
categories, and x-axis maximum.

@param {number[][]} data - The numerical data for each bar section. Each array represents a series for the stack.
@param {string[]} categories - The names of the categories displayed on the y-axis.
@param {number} xAxisMax - The maximum value for the x-axis. If data exceeds this value, the axis expands to fit.
-->

<script lang="ts">
  import { onMount } from "svelte";
  import * as echarts from "echarts";

  import { colorPalette } from "$lib/components/visual/constants";

  /**
   * The numerical data for each bar section. Each array represents a series in
   * the stack.
   *
   * @type {number[][]}
   */
  export let data: number[][] = [
    [10, 20, 30, 40],
    [15, 25, 35, 45],
    [5, 10, 15, 20],
  ];

  /**
   * The names of the categories displayed on the y-axis.
   *
   * @type {string[]}
   */
  export let categories: string[] = [
    "Category A",
    "Category B",
    "Category C",
    "Category D",
  ];

  /**
   * The maximum value for the x-axis. If data exceeds this value, the axis
   * expands to fit.
   *
   * @type {number}
   */
  export let xAxisMax = 150;

  /** The aspect ratio of the chart container. */
  export let aspect = "aspect-[11/2]";

  let chart: echarts.ECharts | undefined;
  let chartContainer: HTMLDivElement;

  // Define your stacked data and options
  const options: echarts.EChartOption = {
    tooltip: {
      trigger: "axis",
      axisPointer: { type: "shadow" },
    },
    xAxis: {
      type: "value",
      max: (value) => Math.max(xAxisMax, value.max),
    },
    yAxis: {
      type: "category",
      data: categories,
      axisLabel: {
        interval: 0, // Ensure labels aren't skipping any category
        fontSize: 16,
        align: "left",
        padding: [0, 100, 0, 0],
        margin: 100,
      },
      axisLine: {
        show: false,
      },
    },
    series: data.map((seriesData, index) => ({
      name: `Part ${index + 1}`,
      type: "bar",
      stack: "total",
      data: seriesData,
      itemStyle: { color: colorPalette[index] }, // Use a color array to pick different colors
      barCategoryGap: "0%",
    })),
    grid: {
      top: 20, // Increase top padding
      bottom: 20, // Increase bottom padding
      left: 100,
      right: 20,
    },
  };

  // Initialize the chart
  onMount(() => {
    chart = echarts.init(chartContainer);
    chart.setOption(options);

    // Optional: Handle chart resizing
    window.addEventListener("resize", () => {
      chart?.resize();
    });

    return () => {
      chart?.dispose();
      window.removeEventListener("resize", () => chart?.resize());
    };
  });
</script>

<!-- Chart container -->
<div class={aspect} bind:this={chartContainer} />
