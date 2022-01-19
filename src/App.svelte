<script>
	import { fade, fly } from 'svelte/transition';

	const swap = {'X':'O', 'O':'X'};
	
	var cur_char, board, victory, victor, turn_count, tie;

	function initialize() {
		turn_count = 1;
		cur_char = 'X';
		board = [
			['', '', ''],
			['', '', ''],
			['', '', ''],
		]
		victory = false;
		victor = null;
		tie = false;
	}


	function makeMove(i, j) {
		board[i][j] = cur_char;
		checkVictory(i, j);
		cur_char = swap[cur_char];
		turn_count++;
		tie = turn_count >= 10 && !victory;
	}

	function checkVictory(i, j) {
		let col = true;
		let row = true;
		let diag1 = true;
		let diag2 = true;

		for(let x = 0; x < 3; x++){
			if(board[i][x] !== cur_char) col = false;
			if(board[x][j] !== cur_char) row = false;

			// diagonals
			if(board[x][x] !== cur_char) diag1 = false;
			if(board[x][2-x] !== cur_char) diag2 = false;
		}
		
		victory = col || row || diag1 || diag2;
		if(victory){
			victor = cur_char;
		}
	}

	initialize();

</script>

<main>
	<h1>Tic Tac Toe</h1>
	{#if !victory && !tie}
		{cur_char}'s turn to go
		<div out:fade="{{duration: 300}}" class="board">
			{#each board as rows, i}
				{#each rows as tile, j}
					<div class="tile" on:click|once={() => makeMove(i, j)}>
						<h1>
							{tile}
						</h1>
					</div>
				{/each}
			{/each}
		</div>
	{:else}
		<div class="win-banner" in:fly="{{y:600, duration: 600, delay:300}}">
			{#if tie}
				Tie!
			{:else}
				{victor} wins!
			{/if}
		</div>
		<button in:fade="{{delay:900, duration: 500}}" class="btn" on:click={() => initialize()}>Play Again 🔄</button>
	{/if}
	
</main>

<style>
	main {
		text-align: center;
		overflow: hidden;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	.tile {
		border: 1px solid black;
		height: 8rem;
		width: 8rem;
		display: flex;
		justify-content: center; /* align horizontal */
		align-items: center; /* align vertical */
		transition: ease-out 200ms;
	}

	.tile:hover {
		background-color: lightskyblue;
		cursor: pointer;
	}

	.win-banner {
		display: flex;
		justify-content: center; /* align horizontal */
		align-items: center;
		font-size: 6rem;
		padding: 1rem;
	}

	.btn {
		font-size: 2rem;
		padding: 1rem;
		border-color: skyblue;
		border-radius: 0.7rem;
		background-color: lightskyblue;
		transition: ease-in-out 200ms;
	}

	.btn:hover {
		cursor: pointer;
		transform: scale(1.1);
	}


	.board {
		display: grid;
		grid-template-columns: 8rem 8rem 8rem;
		max-width: 24rem;
		margin: 0 auto;
	}

</style>