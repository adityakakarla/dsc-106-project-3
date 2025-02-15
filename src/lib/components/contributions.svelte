<script lang="ts">
	import Drawer from 'svelte-drawer-component';
	let { open } = $props();
</script>

<button
	onclick={() => (open = true)}
	class="cursor-pointer rounded-md bg-red-900 hover:bg-stone-900 px-3 py-1.5 text-sm text-white transition duration-300 "
	>Contributions</button
>
<Drawer {open} size="30%" placement="left" on:clickAway={() => (open = false)}>
	<div class="p-10 flex flex-col">
		<p>Sample contributions.</p>
        <button onclick={() => (open = false)} class='cursor-pointer rounded-md bg-red-900 hover:bg-stone-900 px-3 py-1.5 text-sm text-white transition duration-300 w-fit'>Close</button>
	</div>
</Drawer>
