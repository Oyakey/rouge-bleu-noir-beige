<script>
	let starter;
	let grid = generate(Math.round(Math.random() * 1000));

	function generate(seed) {
		const grid = [
			...Array(8).fill('red'),
			...Array(8).fill('blue'),
			'black',
			...Array(7).fill('beige')
		];

		if (seed % 2 === 0) {
			grid.push('red');
			starter = 'red';
		} else {
			grid.push('blue');
			starter = 'blue';
		}

		for (let i = grid.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[grid[i], grid[j]] = [grid[j], grid[i]];
		}

		return grid;
	}

</script>

<section class="h-0 relative -top-24 flex items-baseline gap-4 w-0 
whitespace-nowrap">
	<h1>rbnb</h1><span class="text-base">(rouge, bleu, noir, beige)</span>
</section>

<p class="mb-8 text-xl">
	<span class={[
		starter === 'red' ? 'text-red-500' : 'text-blue-500',
		'font-bold'
		].join(' ')}>
		L'équipe {starter === 'red' ? 'rouge' : 'bleue'}
	</span>
	commence.
</p>

<div class="grid grid-cols-5 gap-2 mb-8">
	{#each grid as box}
	<div class={[
		'h-full w-full aspect-square rounded', 
		box === 'red' && 'bg-red-500',
		box === 'blue' && 'bg-blue-500',
		box === 'beige' && 'bg-orange-200',
		box === 'black' && 'bg-black',
	].join(' ')}></div>
	{/each}
</div>

<button class="bg-neutral-100 dark:bg-neutral-900" id="regenerate" type="button" on:click={()=>{
	grid = generate(Math.round(Math.random() * 1000));
}}>
	Générer une nouvelle carte clé.
</button>