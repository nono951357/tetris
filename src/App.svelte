<script>
  import { onMount } from 'svelte';

  let pálya = Array.from({ length: 20 }, () =>
    Array.from({ length: 10 }, () => 0)
  );

  const alakzatok = [
    [
      [1, 1],
      [1, 1]
    ],
    [
      [2, 2, 2, 2]
    ],
    [
      [3, 3, 3],
      [0, 3, 0]
    ],
    [
      [4, 0, 0],
      [4, 4, 4]
    ],
    [
      [0, 0, 5],
      [5, 5, 5]
    ],
    [
      [6, 6, 0],
      [0, 6, 6]
    ],
    [
      [0, 7, 7],
      [7, 7, 0]
    ]
  ];

  let ap = [0, 4];
  let aa = 2;
  let idozites;
  let gameStarted = false;
  let score = 0;

  function resetGame() {
    pálya = Array.from({ length: 20 }, () =>
      Array.from({ length: 10 }, () => 0)
    );
    ap = [0, 4];
    aa = Math.floor(Math.random() * alakzatok.length);
    score = 0;
    startTimer();
  }

  function startTimer() {
    idozites = setInterval(() => {
      ap[0]++;
      if (collision()) {
        ap[0]--;
        merge();
        clearRows();
        ap = [0, 4];
        aa = [0, 1, 2, 3, 4, 5, 6].sort((a, b) => Math.random() - 0.5)[0];
        if (collision()) {
          clearInterval(idozites);
          resetGame();
        }
      }
    }, 1000);
  }

  function collision() {
    for (let i = 0; i < alakzatok[aa].length; i++) {
      for (let j = 0; j < alakzatok[aa][i].length; j++) {
        if (
          alakzatok[aa][i][j] &&
          (pálya[ap[0] + i] && pálya[ap[0] + i][ap[1] + j]) !== 0
        ) {
          return true;
        }
      }
    }
    return false;
  }

  function merge() {
    for (let i = 0; i < alakzatok[aa].length; i++) {
      for (let j = 0; j < alakzatok[aa][i].length; j++) {
        if (alakzatok[aa][i][j]) {
          pálya[ap[0] + i][ap[1] + j] = alakzatok[aa][i][j];
        }
      }
    }
  }

  function clearRows() {
    let linesCleared = 0;
    pálya = pálya.filter(row => {
      if (row.every(cell => cell !== 0)) {
        linesCleared++;
        return false;
      }
      return true;
    });
    while (pálya.length < 20) {
      pálya.unshift(Array(10).fill(0));
    }
    score += linesCleared * 10 * linesCleared;
  }

  function rotate() {
    const fa = alakzatok[aa][0].map((_, i) =>
      alakzatok[aa].map(row => row[i]).reverse()
    );
    const oldAA = alakzatok[aa];
    alakzatok[aa] = fa;
    if (collision()) {
      alakzatok[aa] = oldAA;
    }
  }

  function startGame() {
    gameStarted = true;
    startTimer();
  }

  onMount(() => {
    // Do not start the timer on mount
  });
</script>

<main>
  <div class="palya">
    {#each pálya as sor, i}
      {#each sor as oszlop, j}
        {#if i >= ap[0] && i < ap[0] + alakzatok[aa].length && j >= ap[1] && j < ap[1] + alakzatok[aa][0].length && alakzatok[aa][i - ap[0]][j - ap[1]]}
          <div class="elem {`e${alakzatok[aa][i - ap[0]][j - ap[1]]}`}"></div>
        {:else}
          <div class="elem {`e${oszlop}`}"></div>
        {/if}
      {/each}
    {/each}
  </div>
  <div>Score: {score}</div>
  <button on:click={startGame} tabindex="0" on:keydown={e => {
    if (!gameStarted) return;
    if (e.key === 'ArrowLeft' && ap[1] > 0) {
      ap[1]--;
      if (collision()) {
        ap[1]++;
      }
    }
    if (e.key === 'ArrowRight' && ap[1] + alakzatok[aa][0].length < pálya[0].length) {
      ap[1]++;
      if (collision()) {
        ap[1]--;
      }
    }
    if (e.key === 'ArrowDown') {
      ap[0]++;
      if (collision()) {
        ap[0]--;
      }
    }
    if (e.key === 'ArrowUp') {
      rotate();
    }
  }}>Start</button>
</main>

<style>
  .palya {
    display: grid;
    grid-template-columns: repeat(10, 1fr);
    grid-template-rows: repeat(20, 1fr);
    border: 1px solid rgb(219, 107, 107);
    margin: 10px;
  }
  .elem {
    border: 1px solid black;
    width: 20px;
    height: 20px;
    text-align: center;
    background-color: #2bc9c9;
  }
  .e0 {
    background-color: rgb(35, 6, 3);
  }
  .e1 {
    background-color: rgb(0, 229, 255);
  }
  .e2 {
    background-color: rgb(0, 229, 255);
  }
  .e3 {
    background-color: rgb(183, 255, 0);
  }
  .e4 {
    background-color: rgb(208, 0, 255);
  }
  .e5 {
    background-color: rgb(255, 115, 0);
  }
  .e6 {
    background-color: rgb(0, 8, 255);
  }
  .e7 {
    background-color: rgb(255, 188, 0);
  }
</style>