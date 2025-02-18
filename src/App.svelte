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
	// https://stackoverflow.com/a/2450976/12765719
	function shuffle(array) {
		let currentIndex = array.length;

		// While there remain elements to shuffle...
		while (currentIndex != 0) {
			// Pick a remaining element...
			let randomIndex = Math.floor(Math.random() * currentIndex);
			currentIndex--;

			// And swap it with the current element.
			[array[currentIndex], array[randomIndex]] = [
				array[randomIndex],
				array[currentIndex],
			];
		}

		return array;
	}
	const pattern = {
		'4x4': [
			[1, 2, 3, 4],
			[5, 6, 7, 8],
			[9, 10, 11, 12],
			[13, 14, 15, null],
		],
		'๔x๓': [
			['๑', '๒', '๓'],
			['๔', '๕', '๖'],
			['๗', '๘', '๙'],
			['๑๐', '๑๑', null],
		],
		'三乘三': [
			['一', '二', '三'],
			['四', '五', '六'],
			['七', '八', null],
		]
	}
	const keys = Object.keys(pattern)

	let board = $state(keys[0])
	let cubes = $state(pattern[keys[0]]);
	let tiles = $state([]);
	let countMove = $state(0);

	function restart(pat = pattern[board]) {
		let flatGrid = pat.flat();
		
		flatGrid = shuffle(flatGrid);
		
		tiles = []
		pat.forEach((disc, rowindex) => {
			disc.forEach((value, colindex) => {
				if (!tiles[rowindex]) {
					tiles[rowindex] = []
				}
				tiles[rowindex][colindex] = flatGrid.pop();
			});
		});

		cubes = pat
		countMove = 0;
	}
	function handleMove(rowmove, colmove) {
		let [rowindex, colindex] = indicate(tiles);
		if (rowmove == rowindex) {
			if (colmove > colindex) {
				// left
				let applies = [];
				tiles[rowindex].forEach((pile) => {
					if (pile) {
						applies.push(pile);
					}
				});
				applies.splice(colmove, 0, null);
				applies.forEach((apply, colindex) => {
					tiles[rowindex][colindex] = apply;
				});
				countMove++;
			} else if (colmove < colindex) {
				// right
				let applies = [];
				tiles[rowindex].forEach((pile) => {
					if (pile) {
						applies.push(pile);
					}
				});
				applies.splice(colmove, 0, null);
				applies.forEach((apply, colindex) => {
					tiles[rowindex][colindex] = apply;
				});
				countMove++;
			}
		} else if (colmove == colindex) {
			if (rowmove > rowindex) {
				// up
				let applies = [];
				tiles.forEach((piles) => {
					if (piles[colindex]) {
						applies.push(piles[colindex]);
					}
				});
				applies.splice(rowmove, 0, null);
				applies.forEach((apply, rowindex) => {
					tiles[rowindex][colindex] = apply;
				});
				countMove++;
			} else if (rowmove < rowindex) {
				// down
				let applies = [];
				tiles.forEach((piles) => {
					if (piles[colindex]) {
						applies.push(piles[colindex]);
					}
				});
				applies.splice(rowmove, 0, null);
				applies.forEach((apply, rowindex) => {
					tiles[rowindex][colindex] = apply;
				});
				countMove++;
			}
		}
	}

	onMount(() => {
		restart();
	});
</script>

<div class="flex flex-wrap justify-center items-center gap-6 p-4">
	<label class="">
		<select class="cursor-pointer bg-neutral-900 text-neutral-50 text-center" oninput={(e) => {
			board = e.target.value
			restart(pattern[board])
		}}>
		{#each keys as key}
			<option value={key}>{key}</option>
		{/each}
		</select>
	</label>
	<button
		class="cursor-pointer font-semibold text-lg border-b-2 border-violet-600 hover:border-transparent hover:text-yellow-300"
		onclick={() => {
			restart();
		}}
	>
		Restart
	</button>
	<span class="">Moves: {countMove}</span>
</div>

<div
	class="flex flex-col justify-center items-center p-4 text-center font-semibold text-4xl font-mono w-fit mx-auto text-neutral-900"
>
	{#each tiles as piles, rowindex (rowindex)}
		<div class="flex">
			{#each piles as pile, colindex (`${rowindex}-${colindex}`)}
				<button
					class="cursor-pointer block border w-22 h-22 border-violet-600 transition {pile ==
					null
						? 'bg-violet-600'
						: cubes[rowindex][colindex] == pile
							? 'bg-yellow-300'
							: 'bg-neutral-100 hover:bg-white'}"
					onclick={() => {
						handleMove(rowindex, colindex);
					}}
					>{pile}
				</button>
			{/each}
		</div>
	{/each}
</div>
