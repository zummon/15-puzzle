<script>
	import { onMount } from "svelte";

	// https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random
	function getRandomInt(max) {
		return Math.floor(Math.random() * max);
	}
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
		tiles = pat
		
		;[...Array(300)].forEach(() => {
			let ranRow = getRandomInt(pat.length)
			let ranCol = getRandomInt(pat[0].length)
			handleMove(ranRow, ranCol)
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
					class="cursor-pointer block border w-[88px] h-[88px] border-violet-600 transition {pile ==
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
