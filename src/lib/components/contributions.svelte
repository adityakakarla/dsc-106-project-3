<script lang="ts">
	import Drawer from 'svelte-drawer-component';
	let open = false;
</script>

<button
	onclick={() => (open = true)}
	class="cursor-pointer rounded-md bg-red-900 px-3 py-1.5 text-sm text-white transition duration-300 hover:bg-stone-900"
>
	Development Process/Contributions
</button>

<Drawer {open} size="30%" placement="left" on:clickAway={() => (open = false)}>
	<div class="flex flex-col space-y-4 p-10">
		<h2 class="text-lg font-bold">Contributions</h2>
		<p>
			Our team worked collaboratively on a single computer to streamline development and avoid merge
			conflicts. This allowed for real-time debugging and faster iteration cycles. The project was
			completed in approximately 15 hours, with time distributed as follows:
		</p>
		<ul class="list-inside list-disc">
			<li><strong>Exploratory Data Analysis & Preprocessing:</strong> ~3 hours</li>
			<li><strong>D3.js Visualization Development:</strong> ~7 hours</li>
			<li><strong>Interaction Features (Brushing, Tooltips, Aggregation):</strong> ~4 hours</li>
			<li><strong>Testing, Debugging, & Deployment:</strong> ~1 hour</li>
		</ul>

		<h3 class="text-md font-semibold">Challenges Faced and Lessons Learned</h3>
		<ul class="list-inside list-disc">
			<li>
				<strong>Handling D3.js Complexity:</strong> D3 has a steep learning curve, particularly when
				implementing interactions. Debugging issues related to brushing and dropdown selection required
				significant trial and error.
			</li>
			<li>
				<strong>User Experience Considerations:</strong> Early iterations were cluttered, leading us
				to simplify interactions and refine our UI for better usability.
			</li>
		</ul>

		<h3 class="text-md font-semibold">Future Improvements</h3>
		<ul class="list-inside list-disc">
			<li>
				Implement additional filters for finer control over the dataset (e.g., filtering by meal
				type).
			</li>
			<li>Allow users to compare multiple nutrients simultaneously to identify correlations.</li>
			<li>Improve mobile responsiveness to expand accessibility.</li>
		</ul>

		<h3 class="text-md font-semibold">Contributions</h3>
		<ul class="list-inside list-disc">
			<li>
				<strong>Aditya:</strong> Oversaw development, conceptualized the idea, conducted exploratory
				data analysis, assisted in interaction features, and deployed the web app. Additionally, helped
				Michael, Pranav, and Rohan with their tasks to ensure cohesiveness.
			</li>
			<li>
				<strong>Michael:</strong> Implemented the main D3.js graph, performed data aggregations, and
				wrote the initial project write-up. These aggregations included calculating the average intake
				per hour for everyone in the dataset.
			</li>
			<li>
				<strong>Pranav:</strong> Developed the brushing interaction and implemented the aggregation feature
				displayed at the bottom of the page.
			</li>
			<li>
				<strong>Rohan:</strong> Created the tooltip feature and ensured adherence to expressiveness criteria
				to prevent data misinterpretation.
			</li>
		</ul>

		<h3 class="text-md font-semibold">Conclusion</h3>
		<p>
			Our interactive visualization provides a clear and engaging way to analyze late-night eating
			habits. By combining effective visual encodings with intuitive interaction techniques, we
			offer users a tool to uncover meaningful patterns in their dietary behaviors. Through
			thoughtful design choices and collaborative development, we successfully created a data-driven
			narrative that is both informative and user-friendly.
		</p>

		<button
			onclick={() => (open = false)}
			class="w-fit cursor-pointer rounded-md bg-red-900 px-3 py-1.5 text-sm text-white transition duration-300 hover:bg-stone-900"
		>
			Close
		</button>
	</div>
</Drawer>
