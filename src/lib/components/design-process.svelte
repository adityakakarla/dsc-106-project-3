<script lang="ts">
	import Drawer from 'svelte-drawer-component';
	let open = false;
</script>

<button
	onclick={() => (open = true)}
	class="cursor-pointer rounded-md bg-red-900 px-3 py-1.5 text-sm text-white transition duration-300 hover:bg-stone-900"
>
	Design Rationale
</button>

<Drawer {open} size="30%" placement="left" on:clickAway={() => (open = false)}>
	<div class="flex flex-col space-y-4 p-10">
		<h2 class="text-lg font-bold">Design Rationale</h2>

		<h3 class="text-md font-semibold">Motivation</h3>
		<p>
			Understanding hourly nutrient intake patterns provides valuable insights into eating habits,
			particularly for health-conscious individuals, nutritionists, and researchers. These insights
			can also benefit restaurants and business owners seeking to optimize their operating hours
			based on consumer dietary patterns.
		</p>
		<p>
			By examining spikes in nutrient consumption during late hours, businesses can make data-driven
			decisions on when to remain open and what type of food to offer during these periods. These
			insights help maximize revenue while catering to consumer needs.
		</p>

		<h4 class="text-md font-semibold">Key Questions Addressed:</h4>
		<ul class="list-inside list-disc">
			<li>What time of day do people consume the most calories and nutrients?</li>
			<li>Are there significant spikes in consumption during late-night hours?</li>
			<li>How does intake of specific nutrients vary throughout the day?</li>
		</ul>
		<p>
			By answering these questions, our visualization serves as a tool for both personal health
			awareness and broader nutritional and business research.
		</p>

		<h3 class="text-md font-semibold">Visual Encodings and Interaction Techniques</h3>

		<h4 class="text-md font-semibold">Bar Chart for Temporal Trends</h4>
		<p>
			We chose a bar chart to represent nutrient intake by the hour instead of a line graph. While
			line charts are commonly used for time-series data, they imply continuity between points.
			Since eating patterns are discrete events, a bar chart provides a clearer representation of
			individual hourly differences, making it easier to compare nutrient intake at different times
			of the day.
		</p>

		<h4 class="text-md font-semibold">Dynamic Text and Brushing</h4>
		<p>
			To enhance exploration, we implemented a brushing feature, allowing users to highlight
			specific time ranges. This interaction enables users to examine subsets of the day and compare
			them to the overall average, helping them identify trends such as late-night calorie surges.
			The dynamic text updates in real-time, displaying:
		</p>
		<ul class="list-inside list-disc">
			<li><strong>Total daily average intake</strong></li>
			<li><strong>Average intake for the selected hours</strong></li>
		</ul>
		<p>
			These numerical insights complement the graphical representation, allowing for precise
			analysis.
		</p>

		<h4 class="text-md font-semibold">Nutrient Selection Dropdown</h4>
		<p>
			Instead of cluttering the visualization with multiple graphs, we implemented a dropdown menu
			that allows users to select which nutrient they wish to explore. This approach reduces
			cognitive overload, making the visualization more user-friendly and adaptable to various
			dietary interests.
		</p>

		<h4 class="text-md font-semibold">Tooltip Feature</h4>
		<p>
			To facilitate quick insights without requiring users to brush over specific time ranges, we
			added a tooltip that displays the average nutrient intake for each hour when hovered over.
			This feature ensures that users can easily explore data points without interacting with the
			brush tool.
		</p>

		<h4 class="text-md font-semibold">Color Choices and Expressiveness</h4>
		<p>
			We carefully selected the colors to maintain clarity, avoid misinterpretations, and prevent
			visual clutter. The entire website follows a deep red color palette, which is soft on the
			eyes, ensures readability, and allows information to be conveyed clearly to the user. The red
			fade from the bottom brings out the bar graph a bit more and also makes the page more
			aesthetically pleasing to look at.
		</p>

		<button
			onclick={() => (open = false)}
			class="w-fit cursor-pointer rounded-md bg-red-900 px-3 py-1.5 text-sm text-white transition duration-300 hover:bg-stone-900"
		>
			Close
		</button>
	</div>
</Drawer>
