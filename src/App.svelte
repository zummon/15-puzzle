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
		"4x4": [
			[1, 2, 3, 4],
			[5, 6, 7, 8],
			[9, 10, 11, 12],
			[13, 14, 15, null],
		],
		"๔x๓": [
			["๑", "๒", "๓"],
			["๔", "๕", "๖"],
			["๗", "๘", "๙"],
			["๑๐", "๑๑", null],
		],
		三乘三: [
			["一", "二", "三"],
			["四", "五", "六"],
			["七", "八", null],
		],
	};
	const keys = Object.keys(pattern);

	let board = $state(keys[0]);
	let cubes = $state(pattern[keys[0]]);
	let tiles = $state([]);
	let countMove = $state(0);

	function restart(pat = pattern[board]) {
		tiles = pat;
		[...Array(300)].forEach(() => {
			let ranRow = getRandomInt(pat.length);
			let ranCol = getRandomInt(pat[0].length);
			handleMove(ranRow, ranCol);
		});
		cubes = pat;
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

<div class="max-w-md mx-auto my-8 px-4 flex flex-col items-center gap-6">
	<div
		class="w-full flex flex-wrap justify-between items-center gap-4 bg-brand-light-board/40 dark:bg-brand-dark-board/40 backdrop-blur-md p-4 rounded-2xl shadow-xs border border-brand-light-text/5 dark:border-brand-dark-text/5 transition-all duration-300"
	>
		<label class="flex items-center gap-2">
			<span class="text-xs font-bold uppercase tracking-wider opacity-60"
				>Grid:</span
			>
			<select
				class="cursor-pointer bg-brand-light-tile dark:bg-brand-dark-tile border-2 border-brand-light-text/10 dark:border-brand-dark-text/10 rounded-xl px-3 py-1.5 font-bold font-mono text-sm shadow-xs transition-all duration-200 focus:outline-hidden focus:border-brand-light-accent dark:focus:border-brand-dark-accent"
				oninput={(e) => {
					board = e.target.value;
					restart(pattern[board]);
				}}
			>
				{#each keys as key}
					<option value={key} selected={board === key}>{key}</option>
				{/each}
			</select>
		</label>

		<div class="flex items-center gap-4">
			<div
				class="bg-brand-light-tile dark:bg-brand-dark-tile px-4 py-1.5 rounded-xl border-2 border-brand-light-text/10 dark:border-brand-dark-text/10 shadow-inner font-mono text-sm font-bold"
			>
				Moves: <span class="text-brand-light-accent dark:text-brand-dark-accent"
					>{countMove}</span
				>
			</div>

			<button
				class="cursor-pointer font-bold text-sm bg-brand-light-accent text-white px-4 py-2 rounded-xl shadow-md hover:shadow-lg active:scale-95 active:shadow-xs transition-all duration-200"
				onclick={() => {
					restart();
				}}
			>
				Restart
			</button>
		</div>
	</div>

	<div
		class="p-4 bg-brand-light-board dark:bg-brand-dark-board rounded-3xl shadow-xl border-4 border-brand-light-board dark:border-brand-dark-board flex flex-col gap-2 transition-all duration-300"
	>
		{#each tiles as piles, rowindex (rowindex)}
			<div class="flex gap-2">
				{#each piles as pile, colindex (`${rowindex}-${colindex}`)}
					<button
						class="cursor-pointer block w-20 h-20 sm:w-22 sm:h-22 rounded-xl font-bold text-3xl font-mono transition-all duration-200 active:scale-95 animate-bounce-subtle
						{pile == null
							? 'bg-transparent border-2 border-dashed border-brand-light-text/10 dark:border-brand-dark-text/10 shadow-inner pointer-events-none'
							: cubes[rowindex]?.[colindex] == pile
								? 'bg-brand-light-correct dark:bg-brand-dark-correct text-brand-light-text border-b-4 border-amber-400 dark:border-amber-600 shadow-md transform hover:-translate-y-0.5'
								: 'bg-brand-light-tile dark:bg-brand-dark-tile text-brand-light-text dark:text-brand-dark-text border-b-4 border-neutral-200 dark:border-neutral-900 shadow-md hover:bg-neutral-50 dark:hover:bg-neutral-700/50 transform hover:-translate-y-0.5'}"
						onclick={() => {
							handleMove(rowindex, colindex);
						}}
						disabled={pile == null}
					>
						{pile ?? ""}
					</button>
				{/each}
			</div>
		{/each}
	</div>
</div>
