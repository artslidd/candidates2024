<script lang="ts">
  import Greet from "./lib/Greet.svelte";

  const players = {
    id1: "Gukesh D",
    id2: "Ian Nepomniatchi",
    id3: "Fabiano Caruana",
    id4: "Hikaru Nakamura",
    id5: "Nijat Abasov",
    id6: "Praggnanadha",
    id7: "Alireza Firouzja",
    id8: "Vidit Gujrathi",
  };

  type PlayerId = keyof typeof players;

  const originalPlayerList = Object.keys(players) as PlayerId[];
  let playerResults = [];

  type Game = {
    whitePlayer: PlayerId;
    blackPlayer: PlayerId;
    result: 0 | 0.5 | 1; // 0 is victory for black, 0.5 is draw and 1 is victory for white
  };

  const results: Record<"games", Game[]> = {
    games: [
      { whitePlayer: "id1", blackPlayer: "id2", result: 0.5 },
      { whitePlayer: "id1", blackPlayer: "id3", result: 0.5 },
      { whitePlayer: "id1", blackPlayer: "id4", result: 0.5 },
      { whitePlayer: "id1", blackPlayer: "id5", result: 1 },
      { whitePlayer: "id1", blackPlayer: "id6", result: 0.5 },
      { whitePlayer: "id1", blackPlayer: "id7", result: 1 },
      { whitePlayer: "id1", blackPlayer: "id8", result: 0.5 },
      { whitePlayer: "id2", blackPlayer: "id1", result: 0.5 },
      { whitePlayer: "id2", blackPlayer: "id3", result: 0.5 },
      { whitePlayer: "id2", blackPlayer: "id4", result: 0.5 },
      { whitePlayer: "id2", blackPlayer: "id5", result: 0.5 },
      { whitePlayer: "id2", blackPlayer: "id6", result: 0.5 },
      { whitePlayer: "id2", blackPlayer: "id7", result: 1 },
      { whitePlayer: "id2", blackPlayer: "id8", result: 1 },
      { whitePlayer: "id3", blackPlayer: "id1", result: 0.5 },
      { whitePlayer: "id3", blackPlayer: "id2", result: 0.5 },
      { whitePlayer: "id3", blackPlayer: "id4", result: 0.5 },
      { whitePlayer: "id3", blackPlayer: "id5", result: 1 },
      { whitePlayer: "id3", blackPlayer: "id6", result: 0.5 },
      { whitePlayer: "id3", blackPlayer: "id7", result: 1 },
      { whitePlayer: "id3", blackPlayer: "id8", result: 1 },
      { whitePlayer: "id4", blackPlayer: "id1", result: 0.5 },
      { whitePlayer: "id4", blackPlayer: "id2", result: 0.5 },
      { whitePlayer: "id4", blackPlayer: "id3", result: 1 },
      { whitePlayer: "id4", blackPlayer: "id5", result: 1 },
      { whitePlayer: "id4", blackPlayer: "id6", result: 0.5 },
      { whitePlayer: "id4", blackPlayer: "id7", result: 1 },
      { whitePlayer: "id4", blackPlayer: "id8", result: 0 },
      { whitePlayer: "id5", blackPlayer: "id1", result: 0 },
      { whitePlayer: "id5", blackPlayer: "id2", result: 0.5 },
      { whitePlayer: "id5", blackPlayer: "id3", result: 0.5 },
      { whitePlayer: "id5", blackPlayer: "id4", result: 0.5 },
      { whitePlayer: "id5", blackPlayer: "id6", result: 0 },
      { whitePlayer: "id5", blackPlayer: "id7", result: 0.5 },
      { whitePlayer: "id5", blackPlayer: "id8", result: 0.5 },
      { whitePlayer: "id6", blackPlayer: "id1", result: 0 },
      { whitePlayer: "id6", blackPlayer: "id2", result: 0.5 },
      { whitePlayer: "id6", blackPlayer: "id3", result: 0 },
      { whitePlayer: "id6", blackPlayer: "id4", result: 0 },
      { whitePlayer: "id6", blackPlayer: "id5", result: 1 },
      { whitePlayer: "id6", blackPlayer: "id7", result: 0.5 },
      { whitePlayer: "id6", blackPlayer: "id8", result: 0.5 },
      { whitePlayer: "id7", blackPlayer: "id1", result: 1 },
      { whitePlayer: "id7", blackPlayer: "id2", result: 0.5 },
      { whitePlayer: "id7", blackPlayer: "id3", result: 0.5 },
      { whitePlayer: "id7", blackPlayer: "id4", result: 0 },
      { whitePlayer: "id7", blackPlayer: "id5", result: 1 },
      { whitePlayer: "id7", blackPlayer: "id6", result: 0.5 },
      { whitePlayer: "id7", blackPlayer: "id8", result: 0.5 },
      { whitePlayer: "id8", blackPlayer: "id1", result: 0 },
      { whitePlayer: "id8", blackPlayer: "id2", result: 0 },
      { whitePlayer: "id8", blackPlayer: "id3", result: 0.5 },
      { whitePlayer: "id8", blackPlayer: "id4", result: 1 },
      { whitePlayer: "id8", blackPlayer: "id5", result: 0.5 },
      { whitePlayer: "id8", blackPlayer: "id6", result: 0 },
      { whitePlayer: "id8", blackPlayer: "id7", result: 1 },
    ],
  };

  function calculateScore(
    playerId: PlayerId,
    drawValue: number,
    removedPlayers: { value: PlayerId }[]
  ) {
    let score = 0;
    results.games.forEach((game) => {
      const players = [game.whitePlayer, game.blackPlayer];
      const hasPlayerRemoved =
        removedPlayers.filter((p) => players.includes(p.value)).length > 0;

      if (players.includes(playerId) && !hasPlayerRemoved) {
        if (game.result === 0.5) {
          score +=
            game.whitePlayer === playerId
              ? 1 - drawValue / 100
              : drawValue / 100;
        } else {
          score +=
            game.whitePlayer === playerId ? game.result : 1 - game.result;
        }
      }
    });
    return score;
  }

  let total = 0;
  let drawValue = 50;
  const options: { label: string; value: PlayerId }[] = [
    { label: "With Abasov", value: "id5" },
    { label: "With Alireza", value: "id7" },
  ];
  let selected = options.map(() => true);
  let removedPlayers: typeof options = [];
  $: {
    removedPlayers = options.filter((o, i) => !selected[i]);
    playerResults = originalPlayerList
      .filter((pId) => !removedPlayers.map((item) => item.value).includes(pId))
      .map((id) => {
        return [
          id,
          Math.round(calculateScore(id, drawValue, removedPlayers) * 100) / 100,
        ];
      })
      .sort((x, y) => y[1] - x[1]);
    total =
      (originalPlayerList.length - removedPlayers.length - 1) *
      (originalPlayerList.length - removedPlayers.length);
  }
</script>

<main class="container">
  <h1>Welcome to the Candidates configurator</h1>

  <div class="grid-container">
    <div>
      <h2>Ranking</h2>
      <div class="table-container">
        <table class="table">
          {#each playerResults as item, i}
            <tr class="row">
              <td>{players[item[0]]}: {item[1]}</td>
            </tr>
          {/each}
          <tr class="row">
            <td>Total: {total}</td>
          </tr>
        </table>
      </div>
    </div>
    <div>
      <h2>Configure ranking strategy</h2>
      <div class="slidecontainer">
        <h4>
          Draw point distribution (how many points black win for a draw) --
          Current value: {drawValue / 100}
        </h4>
        <span>
          0
          <input type="range" min="0" max="100" bind:value={drawValue} />
          1
        </span>
      </div>
      {#each options as opt, index}
        <h4>{opt.label}?</h4>
        <span>
          <input type="checkbox" bind:checked={selected[index]} />
          Yes
        </span>
      {/each}
    </div>
  </div>

  <!-- <div class="row">
    <Greet />
  </div> -->
</main>
