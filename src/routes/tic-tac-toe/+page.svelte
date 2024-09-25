<script lang="ts">
  import { onMount } from 'svelte';

  let board = Array(9).fill(null);
  let currentPlayer = 'X';
  let winner = null;
  let gameOver = false;

  function checkWinner(board: (string | null)[]) {
    const lines = [
      [0, 1, 2],
      [3, 4, 5],
      [6, 7, 8],
      [0, 3, 6],
      [1, 4, 7],
      [2, 5, 8],
      [0, 4, 8],
      [2, 4, 6]
    ];

    for (let line of lines) {
      const [a, b, c] = line;
      if (board[a] && board[a] === board[b] && board[a] === board[c]) {
        return board[a];
      }
    }

    return null;
  }

  function handleClick(index: number) {
    if (board[index] || gameOver) return;

    board[index] = currentPlayer;
    board = [...board];

    winner = checkWinner(board);
    if (winner) {
      gameOver = true;
    } else if (!board.includes(null)) {
      gameOver = true;
      winner = 'Draw';
    } else {
      currentPlayer = 'O';
      setTimeout(computerMove, 500);
    }
  }

  function computerMove() {
    if (gameOver) return;

    let availableMoves = board.reduce((acc, cell, index) => {
      if (cell === null) acc.push(index);
      return acc;
    }, [] as number[]);

    let move = availableMoves[Math.floor(Math.random() * availableMoves.length)];
    board[move] = currentPlayer;
    board = [...board];

    winner = checkWinner(board);
    if (winner) {
      gameOver = true;
    } else if (!board.includes(null)) {
      gameOver = true;
      winner = 'Draw';
    } else {
      currentPlayer = 'X';
    }
  }

  function resetGame() {
    board = Array(9).fill(null);
    currentPlayer = 'X';
    winner = null;
    gameOver = false;
  }

  onMount(() => {
    if (currentPlayer === 'O') {
      computerMove();
    }
  });
</script>

<h1>Tic Tac Toe</h1>

<div class="board">
  {#each board as cell, index}
    <button on:click={() => handleClick(index)}>
      {cell ?? ''}
    </button>
  {/each}
</div>

<div class="game-status">
  {#if winner}
    <p class="winner-text">
      {winner === 'Draw' ? "It's a draw!" : `${winner} wins!`}
    </p>
  {:else}
    <p>Current player: {currentPlayer}</p>
  {/if}
</div>

<div class="reset-container">
  <button class="reset-button" on:click={resetGame}>Reset Game</button>
</div>

<style>
  .board {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 5px;
    width: 300px;
    margin: 20px auto;
  }

  .board button {
    width: 100px;
    height: 100px;
    font-size: 2em;
    background-color: #f0f0f0;
    border: 1px solid #ccc;
    cursor: pointer;
  }

  .board button:hover {
    background-color: #e0e0e0;
  }

  h1, p {
    text-align: center;
  }

  .game-status {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-top: 20px;
  }

  .winner-text {
    font-size: 1.2em;
    font-weight: bold;
    margin-bottom: 10px;
  }

  .reset-container {
    display: flex;
    justify-content: center;
    margin-top: 20px;
  }

  .reset-button {
    width: auto;
    height: auto;
    font-size: 1em;
    padding: 10px 20px;
    background-color: #4CAF50;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }

  .reset-button:hover {
    background-color: #45a049;
  }
</style>