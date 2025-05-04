<script>
	const algorithms = ['bubble', 'selection', 'insertion', 'merge', 'quick', 'heap'];
</script>

<div class="navbar bg-base-100 shadow-sm">
	<div class="flex-1">
		<a href="/" class="btn btn-ghost text-xl"
			><span class="text-blue-600">Algorithm</span><span class="text-orange-500">Visualizer</span
			></a
		>
	</div>
	<div class="flex-none">
		<ul class="menu menu-horizontal px-1">
			<li>
				<details>
					<summary>Algorithms</summary>
					<ul class="bg-base-100 rounded-t-none p-2">
						{#each algorithms as algorithm}
							<li><a href="/{algorithm}">{algorithm}</a></li>
						{/each}
					</ul>
				</details>
			</li>
		</ul>
	</div>
</div>
