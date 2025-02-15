<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  onMount(() => {
    const data = [10, 20, 30, 40, 50];

    const width = 400;
    const height = 200;

    const svg = d3.select("#chart")
      .append("svg")
      .attr("width", width)
      .attr("height", height);

    svg.selectAll("rect")
      .data(data)
      .enter()
      .append("rect")
      .attr("x", (d, i) => i * 80)
      .attr("y", d => height - d * 3)
      .attr("width", 50)
      .attr("height", d => d * 3)
      .attr("fill", "steelblue");
  });
</script>

<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://svelte.dev/docs/kit">svelte.dev/docs/kit</a> to read the documentation</p>

<div id="chart"></div>
