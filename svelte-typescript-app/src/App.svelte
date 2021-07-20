<script lang="ts">
	import * as _ from 'lodash';  
	import { onMount } from 'svelte';

	enum Players {
		X = "X",
		O = "O"
	}

	const boxes: number[] = _.range(0,9);
	let isFinished: boolean = false;
	let hasWinner: boolean = false;
	let playerTurn: Players = Players.X;
	let boardEl: HTMLElement = document.getElementById("board");

	onMount(() => {
		boardEl = document.getElementById("board");
	});
	
	function placeMark(e: any): void {
		// Tile is occupied
		if (e.currentTarget.innerHTML !== "") {
			return;
		}

		e.currentTarget.innerHTML = playerTurn;
		e.currentTarget.setAttribute("data-mark", playerTurn);

		
		hasWinner = checkWin();
		if (hasWinner) {		
			isFinished = hasWinner;
			return;
		}

		// Game continues
		if (playerTurn === Players.X) {
			playerTurn = Players.O;
		} else {
			playerTurn = Players.X;
		}
	};

	function getElChildren(el: HTMLElement): HTMLElement[] {
		let children: HTMLElement[] = [];

		for (let i = 0; i < el.children.length; i++) {
			children.push(el.children[i] as HTMLElement)
		}

		return children;
	};

	function checkWin(): boolean {
		const tiles: HTMLElement[] = getElChildren(boardEl);

		const marks: string[] = getElChildren(boardEl).map(tile => tile.getAttribute("data-mark"));
		isFinished = tiles.every(x => x.hasAttribute("data-mark"));

		// Create arrays of possible win conditions
		const horizontal = [0,3,6].map(i=> [i,i+1,i+2]);
		const vertical = [0,1,2].map(i=> [i,i+3,i+6]);
		const diagonal = [[0,4,8],[2,4,6]];	
		const winConditions: number[][] = [].concat(horizontal).concat(vertical).concat(diagonal);

		// Find winning condition
		return winConditions.some(win => 
  			marks[win[0]] === playerTurn &&
			marks[win[1]] == playerTurn && 
			marks[win[2]] === playerTurn
		);
	};

	// Reset game and clear placements
	function newGame(): void {
		const tiles: HTMLElement[] = getElChildren(boardEl);
		tiles.forEach(tile => {
			tile.removeAttribute("data-mark");
			tile.innerText = "";
		});
		playerTurn = Players.X;
		isFinished = false;
		hasWinner = false;
	};
</script>

<main>
	<h1>Tic tac toe!</h1>
	<div class="board" id="board">
		{#each boxes as box}
			<div class="box" on:click={placeMark}></div>
		{/each}
	</div>

		<div class="game-notifier">
			{#if isFinished}
				{#if hasWinner} 
					<h2>{playerTurn} wins!</h2>
				{:else} 
					<h2>Tied!</h2>
				{/if}
				<button on:click={newGame}>New game</button>
			{:else}	
				<h2>{playerTurn} Turn</h2>
			{/if}
		</div>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		width: 612px;
		margin: 0 auto;
		max-height: 100vh;
		overflow: hidden;
	}

	.board {
		width: 600px;
		height: 600px;
		margin: 0 auto;
		background-color: #34495e;
		color: #fff;
		border: 6px solid #2c3e50;
		border-radius: 10px;
		display: grid;
		grid-template: repeat(3, 1fr) / repeat(3, 1fr);
	}

	.box {
		border: 6px solid #2c3e50;
		border-radius: 2px;
		font-family: Helvetica;
		font-weight: bold;
		font-size: 4em;
		display: flex;
		justify-content: center;
		align-items: center;
		cursor: pointer;
	}

	.game-notifier {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	h2 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 3em;
		font-weight: 100;
	}

	button {
		background-color: #ff3e00;
		color: #fff;
		margin: 0;
		text-transform: uppercase;
		padding: 10px 20px;
		outline: 0;
		border: 0;
		cursor: pointer;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>