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

<div class="game-container">
	<div class="controls-panel">
		<label class="control-group">
			<span class="control-label">Grid:</span>
			<select
				class="grid-select"
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

		<div class="right-controls">
			<div class="moves-counter">
				Moves: <span class="moves-number">{countMove}</span>
			</div>

			<button class="restart-btn" onclick={() => restart()}> Restart </button>
		</div>
	</div>

	<div class="puzzle-board">
		{#each tiles as piles, rowindex (rowindex)}
			<div class="board-row">
				{#each piles as pile, colindex (`${rowindex}-${colindex}`)}
					<button
						class="tile animate-tile
						{pile == null
							? 'tile-empty'
							: cubes[rowindex]?.[colindex] == pile
								? 'tile-correct'
								: 'tile-normal'}"
						onclick={() => handleMove(rowindex, colindex)}
						disabled={pile == null}
					>
						{pile ?? ""}
					</button>
				{/each}
			</div>
		{/each}
	</div>
</div>
