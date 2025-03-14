<script lang="ts">
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import Contributions from '$lib/components/contributions.svelte';
  import DesignProcess from '$lib/components/design-process.svelte';

  let { data } = $props();
  let d3Data = $derived(data.data);

  let contributionsOpen = $state(false);
  let designProcessOpen = $state(false);

  let selectedNutrient = $state('sugar');
  let nutrientOptions = $state([
    { value: 'calorie', label: 'Calories (kcal)', unit: 'kcal' },
    { value: 'total_carb', label: 'Carbs (g)', unit: 'g' },
    { value: 'dietary_fiber', label: 'Dietary Fiber (g)', unit: 'g' },
    { value: 'sugar', label: 'Sugar (g)', unit: 'g' },
    { value: 'protein', label: 'Protein (g)', unit: 'g' },
    { value: 'total_fat', label: 'Total Fat (g)', unit: 'g' },
  ]);

  let totalValue = $state(0);
  let selectedValue = $state(0);

  $effect(() => {
    if (d3Data && d3Data.length > 0) {
      createChart(d3Data);
    }
  });

  function createChart(data: any[]) {
    const processedData = processData(data);
    const chartDiv = document.getElementById('chart');
    if (!chartDiv) return;

    chartDiv.innerHTML = '';

    const margin = { top: 20, right: 60, bottom: 80, left: 90 };
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
      .domain([0, d3.max(processedData, (d) => d.avgNutrient) || 0])
      .nice()
      .range([height, 0]);

    totalValue = processedData.reduce((sum, d) => sum + d.avgNutrient, 0);

    const brush = d3.brushX()
      .extent([[0, 0], [width, height]])
      .on('brush end', brushed);

    function brushed(event: any) {
      if (!event.selection) {
        selectedValue = 0;
        d3.selectAll('.bar').attr('opacity', 1);
        return;
      }

      const [x0, x1] = event.selection;
      const selected = processedData.filter(d => {
        const barX = x(d.hour.toString());
        return barX !== undefined && 
               barX >= x0 && 
               barX + x.bandwidth() <= x1;
      });

      selectedValue = selected.reduce((sum, d) => sum + d.avgNutrient, 0);

      d3.selectAll('.bar')
        .attr('opacity', (d: any) => {
          const barX = x(d.hour.toString());
          return barX !== undefined && 
                 barX >= x0 && 
                 barX + x.bandwidth() <= x1 ? 1 : 0.3;
        });
    }

    svg.append('g')
      .attr('class', 'brush')
      .call(brush);

    svg
      .append('g')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .attr('class', 'axis')
      .selectAll('text')
      .style('font-size', '14px')
      .style('font-weight', '500');

    svg.append('g')
      .call(d3.axisLeft(y))
      .attr('class', 'axis')
      .selectAll('text')
      .style('font-size', '14px')
      .style('font-weight', '500');

    svg
      .selectAll('.bar')
      .data(processedData)
      .enter()
      .append('rect')
      .attr('class', 'bar')
      .attr('x', (d) => x(d.hour.toString()) ?? 0)
      .attr('y', (d) => y(d.avgNutrient))
      .attr('width', x.bandwidth())
      .attr('height', (d) => height - y(d.avgNutrient))
      .attr('fill', '#880808')
      .on('mouseover', function (event, d) {
        d3.select(this).attr('fill', '#b32424');

        const tooltipWidth = 200;
        const tooltipHeight = 30;
        let tooltipX = x(d.hour.toString()) ?? 0 + x.bandwidth() / 2 - tooltipWidth / 2;
        let tooltipY = y(d.avgNutrient) - tooltipHeight - 10;

        tooltipX = Math.max(0, Math.min(tooltipX, width - tooltipWidth));
        tooltipY = Math.max(0, tooltipY);

        svg
          .append('rect')
          .attr('class', 'tooltip-bg')
          .attr('x', tooltipX)
          .attr('y', tooltipY)
          .attr('width', tooltipWidth)
          .attr('height', tooltipHeight)
          .attr('fill', 'white')
          .attr('stroke', 'gray');

        const selectedOption = nutrientOptions.find((opt) => opt.value === selectedNutrient);
        const unit = selectedOption ? selectedOption.unit : '';

        svg
          .append('text')
          .attr('class', 'tooltip')
          .attr('x', tooltipX + tooltipWidth / 2)
          .attr('y', tooltipY + tooltipHeight / 2 + 5)
          .attr('text-anchor', 'middle')
          .text(
            `Avg ${
              getNutrientLabelFromOption(selectedOption)
            }: ${d.avgNutrient.toFixed(2)} ${unit}`
          );
      })
      .on('mouseout', function () {
        d3.select(this).attr('fill', '#880808');
        svg.select('.tooltip').remove();
        svg.select('.tooltip-bg').remove();
      });

    svg
      .append('text')
      .attr('transform', `translate(${width / 2}, ${height + margin.top + 40})`)
      .style('text-anchor', 'middle')
      .style('font-size', '16px')
      .style('font-weight', '600')
      .text('Hour of Day');

    const selectedOption = nutrientOptions.find((opt) => opt.value === selectedNutrient);
    
    svg
      .append('text')
      .attr('transform', 'rotate(-90)')
      .attr('y', 0 - margin.left + 20)
      .attr('x', 0 - height / 2)
      .attr('dy', '1em')
      .style('text-anchor', 'middle')
      .style('font-size', '16px')
      .style('font-weight', '600')
      .text(`Average ${selectedOption?.label}`);
  }

  function processData(data: any[]) {
    const hourlyData = data.reduce((acc: any, item: any) => {
      const hour = new Date(item.time_begin).getHours();
      if (!acc[hour]) {
        acc[hour] = { totalNutrient: 0, count: 0 };
      }
      acc[hour].totalNutrient += item[selectedNutrient];
      acc[hour].count++;
      return acc;
    }, {});

    return Object.keys(hourlyData).map((hour) => ({
      hour: +hour,
      avgNutrient: hourlyData[hour].totalNutrient / hourlyData[hour].count,
    }));
  }

  function getNutrientLabel(value: string) {
    const option = nutrientOptions.find(opt => opt.value === value);
    if(option?.label.split(' ')[0] === 'Dietary'){
      return 'Dietary Fiber'
    } else if(option?.label.split(' ')[0] === 'Total'){
      return 'Total Fat'
    }
    return option ? option.label.split(' ')[0] : value;
  }

  function getNutrientLabelFromOption(option: any) {
    if(option?.label.split(' ')[0] === 'Dietary'){
      return 'Dietary Fiber'
    } else if(option?.label.split(' ')[0] === 'Total'){
      return 'Total Fat'
    }
    return option ? option.label.split(' ')[0] : '';
  }
</script>

<style>
  .tooltip {
    font-size: 14px;
    fill: black;
  }
  
  .summary-box {
    border: 1px solid #ccc;
    padding: 1rem;
    border-radius: 0.5rem;
    background-color: white;
    margin-top: 1rem;
  }
  
  .summary-value {
    font-size: 1.25rem;
    font-weight: bold;
    color: #880808;
  }

  :global(.axis line),
  :global(.axis path) {
    stroke-width: 1.5;
  }
</style>

<!-- Gradient background container - includes chart and extends slightly below -->
<div class="w-full [background:radial-gradient(125%_125%_at_50%_10%,#fff_40%,#880808_100%)]">
  <div class="flex w-full flex-col items-center p-10">
    <h1 class="mt-10 text-4xl font-bold text-gray-800 text-center">Why The Melt Stays Open Until 3AM:<br/>An Analysis of Late-Night Snacking</h1>
    <p class='mt-4 text-center'>A nutritional analysis of 32 weeks of food logs from Prof. Jessilynn Dunn<br/> at Duke University to understand average hourly eating patterns.</p>

    <div class="my-6 flex space-x-3">
      <Contributions open={contributionsOpen} />
      <DesignProcess open={designProcessOpen} />
    </div>

    <div class="mb-4">
      <label for="nutrient-select" class="block text-sm font-medium text-gray-700">
        Select Nutrient:
      </label>
      <select
        id="nutrient-select"
        bind:value={selectedNutrient}
        class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-indigo-500 focus:ring-indigo-500"
      >
        {#each nutrientOptions as option}
          <option value={option.value}>{option.label}</option>
        {/each}
      </select>
    </div>

    <div id="chart"></div>
    
    <!-- Add padding at the bottom to extend the gradient -->
    <div class="h-16"></div>
  </div>
</div>

<!-- Outside the gradient - white background -->
<div class="w-full bg-white mt-[-4rem]">
  <div class="flex flex-col items-center p-10">
    <div class="summary-box w-full max-w-3xl">
      <div class="grid grid-cols-2 gap-4">
        <div>
          <p class="text-sm text-gray-600 text-center">Average {getNutrientLabel(selectedNutrient)} Per Day:</p>
          <p class="summary-value text-center">
            {totalValue.toFixed(2)} {nutrientOptions.find(opt => opt.value === selectedNutrient)?.unit}
          </p>
        </div>
        <div>
          <p class="text-sm text-gray-600 text-center">Average {getNutrientLabel(selectedNutrient)} For Selected Hours:</p>
          <p class="summary-value text-center">
            {selectedValue.toFixed(2)} {nutrientOptions.find(opt => opt.value === selectedNutrient)?.unit}
          </p>
        </div>
      </div>
    </div>
  </div>
</div>