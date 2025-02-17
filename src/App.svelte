<script>
	import { onMount } from "svelte";

	function indicate(array) {
		let indicate = [];
		for (let rowindex = 0; rowindex < array.length; rowindex++) {
			for (let colindex = 0; colindex < array[rowindex].length; colindex++) {
				if (array[rowindex][colindex] === null) {
					indicate = [rowindex, colindex];
				}
			}
		}
		return indicate;
	}

	const cubes = [
		[1, 2, 3, 4],
		[5, 6, 7, 8],
		[9, 10, 11, 12],
		[13, 14, 15, null],
	];
	let tiles = $state(cubes);
	let countMove = $state(0);

	// gemini.google
	function movin(rowidx, colidx) {
		const emptyRow = tiles.findIndex((r) => r.includes(null));
		const emptyCol = tiles[emptyRow].indexOf(null);

		const directions = [
			[0, 1], // Right
			[0, -1], // Left
			[1, 0], // Down
			[-1, 0], // Up
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
					tiles[rowidx][colidx],
				];
				countMove += 1;
				return;
			}
		}
	}

	function shuffle() {
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

	function handleKeydown(e) {
		let [rowindex, colindex] = indicate(tiles);
		if (e.key == "ArrowLeft") {
			let applies = [];
			tiles[rowindex].forEach((pile) => {
				if (pile) {
					applies.push(pile);
				}
			});
			applies.push(null);
			applies.forEach((apply, colindex) => {
				tiles[rowindex][colindex] = apply;
			});
		} else if (e.key == "ArrowRight") {
			let applies = [null];
			tiles[rowindex].forEach((pile) => {
				if (pile) {
					applies.push(pile);
				}
			});
			applies.forEach((apply, colindex) => {
				tiles[rowindex][colindex] = apply;
			});
		} else if (e.key == "ArrowUp") {
			let applies = [];
			tiles.forEach((piles) => {
				if (piles[colindex]) {
					applies.push(piles[colindex]);
				}
			});
			applies.push(null);
			applies.forEach((apply, rowindex) => {
				tiles[rowindex][colindex] = apply;
			});
		} else if (e.key == "ArrowDown") {
			let applies = [null];
			tiles.forEach((piles) => {
				if (piles[colindex]) {
					applies.push(piles[colindex]);
				}
			});
			applies.forEach((apply, rowindex) => {
				tiles[rowindex][colindex] = apply;
			});
		}
	}

	onMount(() => {
		shuffle();
	});
</script>

<svelte:document
	onkeydown={(e) => {
		handleKeydown(e);
	}}
/>

<div class="text-center p-4">
	<button
		class="cursor-pointer font-semibold text-lg border-b-2 border-violet-600 hover:border-transparent hover:text-violet-300"
		onclick={() => {
			shuffle();
		}}
	>
		Restart
	</button>
	<span class="ml-6 {countMove > 0 ? '' : 'hidden'}">Moves: {countMove}</span>
</div>

<div
	class="grid grid-cols-4 p-4 text-center font-semibold text-4xl font-mono border border-gray-900 w-fit mx-auto text-gray-900"
>
	{#each tiles as piles, rowindex}
		{#each piles as pile, colindex}
			<button
				class="cursor-pointer block border p-6 border-violet-600 {pile == null
					? 'bg-violet-600'
					: cubes[rowindex][colindex] == pile
						? 'bg-yellow-300'
						: 'bg-gray-100'}"
				onclick={() => {
					movin(rowindex, colindex);
				}}
				>{pile}
			</button>
		{/each}
	{/each}
</div>
