<script lang="ts">
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import Contributions from '$lib/components/contributions.svelte';
  import DesignProcess from '$lib/components/design-process.svelte';

  let { data } = $props();
  let d3Data = $derived(data.data);

  let contributionsOpen = $state(false);
  let designProcessOpen = $state(false);

  onMount(() => {
    if (d3Data && d3Data.length > 0) {
      createChart(d3Data);
    }
  });

  $effect(() => {
    if (d3Data && d3Data.length > 0) {
      createChart(d3Data);
    }
  });

  function createChart(data: any[]) {
    const processedData = processData(data);
    const chartDiv = document.getElementById('chart');
    if (!chartDiv) return;

    chartDiv.innerHTML = ''; // Clear previous chart

    const margin = { top: 20, right: 30, bottom: 60, left: 60 }; // Increased bottom and left margins
    const width = 800 - margin.left - margin.right;
    const height = 400 - margin.top - margin.bottom;

    const svg = d3
      .select('#chart')
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    const x = d3
      .scaleBand()
      .domain(processedData.map((d) => String(d.hour)))
      .range([0, width])
      .padding(0.1);

    const y = d3
      .scaleLinear()
      .domain([0, d3.max(processedData, (d) => d.avgCalories) || 0])
      .nice()
      .range([height, 0]);

    svg
      .append('g')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(x));

    svg.append('g').call(d3.axisLeft(y));

    svg
      .selectAll('.bar')
      .data(processedData)
      .enter()
      .append('rect')
      .attr('class', 'bar')
      .attr('x', (d) => x(d.hour.toString()) || 0)
      .attr('y', (d) => y(d.avgCalories))
      .attr('width', x.bandwidth())
      .attr('height', (d) => height - y(d.avgCalories))
      .attr('fill', '#880808')
      .on('mouseover', function (event, d) {
        d3.select(this).attr('fill', '#b32424');

        const tooltipWidth = 160;
        const tooltipHeight = 30;
        const tooltipX = (x(d!.hour.toString()) ?? 0) + x.bandwidth() / 2 - tooltipWidth / 2;
        const tooltipY = y(d.avgCalories) - tooltipHeight - 10;

        svg
          .append('rect')
          .attr('class', 'tooltip-bg')
          .attr('x', tooltipX)
          .attr('y', tooltipY)
          .attr('width', tooltipWidth)
          .attr('height', tooltipHeight)
          .attr('fill', 'white') // Full opaque white
          .attr('stroke', 'gray'); // Add a border

        svg
          .append('text')
          .attr('class', 'tooltip')
          .attr('x', tooltipX + tooltipWidth / 2)
          .attr('y', tooltipY + tooltipHeight / 2 + 5) // Center text vertically
          .attr('text-anchor', 'middle')
          .text(`Avg Calories: ${d.avgCalories.toFixed(2)}`);
      })
      .on('mouseout', function () {
        d3.select(this).attr('fill', '#880808');
        svg.select('.tooltip').remove();
        svg.select('.tooltip-bg').remove();
      });

    svg
      .append('text')
      .attr('transform', `translate(${width / 2}, ${height + margin.top + 40})`) // Adjusted position
      .style('text-anchor', 'middle')
      .text('Hour of Day');

    svg
      .append('text')
      .attr('transform', 'rotate(-90)')
      .attr('y', 0 - margin.left + 20) // Adjusted position
      .attr('x', 0 - height / 2)
      .attr('dy', '1em')
      .style('text-anchor', 'middle')
      .text('Average Calories');
  }

  function processData(data: any[]) {
    const hourlyData = data.reduce((acc: any, item: any) => {
      const hour = new Date(item.time_begin).getHours();
      if (!acc[hour]) {
        acc[hour] = { totalCalories: 0, count: 0 };
      }
      acc[hour].totalCalories += item.calorie;
      acc[hour].count++;
      return acc;
    }, {});

    return Object.keys(hourlyData).map((hour) => ({
      hour: +hour,
      avgCalories: hourlyData[hour].totalCalories / hourlyData[hour].count,
    }));
  }
</script>

<style>
  .tooltip {
    font-size: 14px;
    fill: black;
  }
</style>

<div
  class="absolute inset-0 -z-10 h-full w-full bg-white [background:radial-gradient(125%_125%_at_50%_10%,#fff_40%,#880808_100%)]"
>
  <div class="flex h-full w-full flex-col items-center p-10">
    <h1 class="mt-10 text-6xl font-bold text-gray-800">Nutritional Impact Analysis</h1>

    <div class="my-6 flex space-x-3">
      <Contributions open={contributionsOpen} />
      <DesignProcess open={designProcessOpen} />
    </div>

    <div id="chart"></div>
  </div>
</div>