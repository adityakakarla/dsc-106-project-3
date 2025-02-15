<script lang="ts">
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let { data } = $props();
  let d3Data = $derived(data.data);

  onMount(() => {
    const width = 1200;
    const height = 600;
    const margin = { top: 40, right: 40, bottom: 80, left: 80 };

    // Create the chart
    const svg = d3.select("#chart")
      .append("svg")
      .attr("width", width)
      .attr("height", height);

    // Set up the scales
    const xScale = d3.scaleBand()
      .domain(d3Data.map(d => d.logged_food))
      .range([margin.left, width - margin.right])
      .padding(0.1);

    const yScale = d3.scaleLinear()
      .domain([0, d3.max(d3Data, d => d.sugar) || 0])
      .nice()
      .range([height - margin.bottom, margin.top]);

    // Create the bars
    svg.selectAll(".bar")
      .data(d3Data)
      .enter()
      .append("rect")
      .attr("class", "bar")
      .attr("x", d => xScale(d.logged_food))
      .attr("y", d => {
        // Ensure a valid number is returned, fallback to 0 if undefined
        const sugar = d.sugar ?? 0;
        return yScale(sugar);
      })
      .attr("width", xScale.bandwidth())
      .attr("height", d => {
        // Ensure a valid height, fallback to 0 if undefined
        const sugar = d.sugar ?? 0;
        return height - margin.bottom - yScale(sugar);
      })
      .attr("fill", "steelblue");

    // Add X-axis
    svg.append("g")
      .selectAll(".x-axis")
      .data([0])
      .enter()
      .append("g")
      .attr("transform", `translate(0, ${height - margin.bottom})`)
      .call(d3.axisBottom(xScale))
      .selectAll("text")
      .attr("transform", "rotate(-45)")
      .style("text-anchor", "end");

    // Add Y-axis
    svg.append("g")
      .attr("transform", `translate(${margin.left}, 0)`)
      .call(d3.axisLeft(yScale));
  });
</script>


<div class="absolute inset-0 -z-10 h-full w-full bg-white [background:radial-gradient(125%_125%_at_50%_10%,#fff_40%,#880808_100%)]">
  <div class="flex h-full w-full flex-col items-center p-10">
    <h1 class="mt-10 text-6xl font-bold text-gray-800">Nutritional Impact Analysis</h1>
    <div class="my-6 flex space-x-3">
      <!-- Include your components here -->
    </div>
    <div id="chart"></div>
  </div>
</div>
