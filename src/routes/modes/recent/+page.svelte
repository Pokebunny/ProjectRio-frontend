<script lang="ts">
    export let data;
    import { stadiums } from '$lib/helpers/stadiumName';
    import { getAllTagSets } from '$lib/helpers/tagNames';
    import { tagsets } from '$lib/stores/tagsets';
    import { onMount } from 'svelte';
    import {characters} from '$lib/helpers/characterName';
    import { invalidate } from '$app/navigation';
    // import { sortableTableAction } from 'svelte-legos';
    import { BACKEND, STAT_ENDPOINTS } from '$lib/constants'

    // Access the tagsets data in your component
    let tagsetsData: any[] = []
    $: {
    tagsetsData = $tagsets; // Get the current value of the tagsets store
    // console.log($tagsets)
    }

    onMount(() => {
        getAllTagSets();
        rerunLoadFunction();
    })

    function rerunLoadFunction() {
      setInterval(() => {
        console.log(new Date());
        invalidate(BACKEND + STAT_ENDPOINTS.GAMES);
      }, 30000);
    }
    

    function withinLastDay(timestamp: Number) {
        const currentTime = Date.now() / 1000; // Convert milliseconds to seconds
        const oneDayAgo: Number = currentTime - (60 * 60 * 24); // Subtract one hour in seconds

        return timestamp >= oneDayAgo;
    }

    
  </script>

<div class="head">
  <!-- <h4 style="display:flex;justify-content:center;align-items:center;font-size:x-small;margin-top:0px;margin-bottom:10px;">Click Game Mode/Player for more detailed stats.</h4> -->
  <!-- <h1 style="display:flex;justify-content:center;align-items:center;">  <img src={slice} alt=""></h1> -->
</div>
{#if data.games}
<div class="flex justify-center items-center mx-auto transition-[width] duration-200 w-full ">
  <div class="table-container text-token">
    <h2 class="h2">Recent Games</h2>
<!--    <table class="table table-hover" use:sortableTableAction>-->
    <table class="table table-hover">

    <thead>
        <tr>
          <th>Away Player</th>
          <th>Away Score</th>
          <th>Home Score</th>
          <th>Home Player</th>
          <th>Stadium</th>
          <th>Game Mode</th>
          <th>Game Start Date</th>
        </tr>
      </thead>
      <tbody>
      {#each data.games as games}
      <!-- filter by being a game mode with slice in name -->
        <!-- {#if String(tagsetsData.find(tagset => tagset.id === live.tag_set)?.name).includes("SLICE")} -->
          {#if withinLastDay(games.date_time_start)}
            <tr>
              <td class="player-link">
                <a class="player decoration-transparent" href={`/modes/player/${games.away_user}`}>{games.away_user}</a>
              </td>
              <td>{games.away_score}</td>
              <td>{games.home_score}</td>
              <td class="player-link">
                <a class="player decoration-transparent" href={`/modes/player/${games.home_user}`}>{games.home_user}</a>
              </td>
              <!-- convert stadium id num to string -->
              <td>{stadiums[games.stadium]}</td>

              <!-- convert tag id num to string -->
              <td class="game-mode"><a class="decoration-transparent" href={`/modes/${tagsetsData.find(tagset => tagset.id === games.game_mode)?.name}`}/ladder>{tagsetsData.find(tagset => tagset.id === games.game_mode)?.name || ''}</a></td>
              <td>{new Date(games.date_time_start * 1000).toLocaleString()}</td>

            </tr>
          {/if}
        <!-- {/if} -->
      {/each}
      </tbody>
    </table>
    </div>
  </div>
{/if}


<style>
  td a, a {
    color: rgba(var(--text-neutral-500) / 1) !important;
  }
</style>
