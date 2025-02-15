<script lang='ts'>
  import { onMount } from 'svelte';
  import Drawer from 'svelte-drawer-component'
  import * as d3 from 'd3';
	import Contributions from '$lib/components/contributions.svelte';
	import DesignProcess from '$lib/components/design-process.svelte';

    onMount(() => {
    const data = [
      { category: "A", value: 30 },
      { category: "B", value: 80 },
      { category: "C", value: 45 },
      { category: "D", value: 60 },
      { category: "E", value: 20 },
      { category: "F", value: 90 },
      { category: "G", value: 55 }
    ];

    const width = 500;
    const height = 300;
    const margin = { top: 40, right: 30, bottom: 50, left: 50 };

    const svg = d3.select("#chart")
      .append("svg")
      .attr("width", width)
      .attr("height", height)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    const xScale = d3.scaleBand()
      .domain(data.map(d => d.category))
      .range([0, width - margin.left - margin.right])
      .padding(0.2);

    const yScale = d3.scaleLinear()
      .domain([0, d3.max(data, d => d.value) as number])
      .range([height - margin.top - margin.bottom, 0]);

    // Draw Bars
    svg.selectAll("rect")
      .data(data)
      .enter()
      .append("rect")
      .attr("x", d => xScale(d.category)!)
      .attr("y", d => yScale(d.value))
      .attr("width", xScale.bandwidth())
      .attr("height", d => height - margin.top - margin.bottom - yScale(d.value))
      .attr("fill", "steelblue")
      .on("mouseover", function (event, d) {
        d3.select(this).transition().duration(200).attr("fill", "orange");
      })
      .on("mouseout", function (event, d) {
        d3.select(this).transition().duration(200).attr("fill", "steelblue");
      });

    // X Axis
    svg.append("g")
      .attr("transform", `translate(0,${height - margin.top - margin.bottom})`)
      .call(d3.axisBottom(xScale));

    // Y Axis
    svg.append("g").call(d3.axisLeft(yScale));

    // Axis Labels
    svg.append("text")
      .attr("x", (width - margin.left - margin.right) / 2)
      .attr("y", height - margin.bottom + 20)
      .attr("text-anchor", "middle")
      .style("font-size", "14px")
      .text("Categories");

    svg.append("text")
      .attr("transform", "rotate(-90)")
      .attr("x", -(height - margin.top - margin.bottom) / 2)
      .attr("y", -margin.left + 15)
      .attr("text-anchor", "middle")
      .style("font-size", "14px")
      .text("Value");
  });
  let contributionsOpen = $state(false);
  let designProcessOpen = $state(false);
</script>

<div class="absolute inset-0 -z-10 h-full w-full bg-white [background:radial-gradient(125%_125%_at_50%_10%,#fff_40%,#880808_100%)]">
    <div class='w-full h-full flex flex-col p-10 items-center'>
        <h1 class='text-6xl font-bold mt-10'>Food That Kills</h1>

        <div class='flex space-x-3 my-6'>
            <Contributions open={contributionsOpen}/>
            <DesignProcess open={designProcessOpen}/>    
        </div>
        
        <div id="chart"></div>
    </div>
</div>
