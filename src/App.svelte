<script>
	import { onMount } from "svelte";

	const cubes = [
		[1, 2, 3, 4],
		[5, 6, 7, 8],
		[9, 10, 11, 12],
		[13, 14, 15, null]
	];
	let tiles = $state(cubes)
	let countMove = $state(0)
	let success = $derived.by(() => {
		return cubes.reduce(
			(arr, row, rowidx) => {
				row.forEach((tile, colidx) => {
					arr[rowidx][colidx] = tile == tiles[rowidx][colidx];
				});
				return arr;
			},
			[[], [], [], []]
		);
	})

	// gemini.google
	function movin (rowidx, colidx) {
		countMove += 1;
		const emptyRow = tiles.findIndex((r) => r.includes(null));
		const emptyCol = tiles[emptyRow].indexOf(null);

		const directions = [
			[0, 1], // Right
			[0, -1], // Left
			[1, 0], // Down
			[-1, 0] // Up
		];

		for (const [dr, dc] of directions) {
			const newRow = rowidx + dr;
			const newCol = colidx + dc;

			if (
				newRow >= 0 &&
				newRow < 4 &&
				newCol >= 0 &&
				newCol < 4 &&
				tiles[newRow][newCol] === null
			) {
				[tiles[rowidx][colidx], tiles[newRow][newCol]] = [
					tiles[newRow][newCol],
					tiles[rowidx][colidx]
				];
				return;
			}
		}
	}


	function shuffle () {
		const flatGrid = tiles.flat();

		for (let i = flatGrid.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[flatGrid[i], flatGrid[j]] = [flatGrid[j], flatGrid[i]];
		}

		const shuffledGrid = [];
		for (let i = 0; i < 4; i++) {
			shuffledGrid.push(flatGrid.slice(i * 4, (i + 1) * 4));
		}

		tiles = shuffledGrid;
		countMove = 0;
	}

	onMount(() => {
		shuffle();
	})
</script>


<div class="text-center p-4">
	<button class="font-semibold text-lg border-b-2 border-violet-600 hover:border-transparent text-violet-300" onclick={()=> { shuffle(); }}>
		Restart
	</button>
	<span class="ml-6 {countMove > 0 ? '' : 'hidden'}">Moves: {countMove}</span>
</div>

<div class="grid grid-cols-4 p-4 text-center font-semibold text-4xl font-mono border border-gray-900 w-fit mx-auto text-gray-900">
	{#each tiles as row, rowidx}
		{#each row as tile, colidx}
			<button
				class="block border p-6 border-violet-600 bg-gray-100 {tile == null ? 'bg-violet-600' : (success[rowidx][colidx] ? 'bg-yellow-300' : '')}"
				onclick={()=> { movin(rowidx, colidx); }} >{tile}
			</button>
		{/each}
	{/each}
</div>


