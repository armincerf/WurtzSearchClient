<script>
  import { onMount } from "svelte";
  import algoliasearch from "algoliasearch/lite";

  let searchClient;
  let index;

  let query = "";
  let hits = [];
  let extraHits = [];
  let totalHits = 0;
  let appID = "XMYY7X6YSY";
  let apiKey = "e78bc0e48fb18f5972e9e8710d269911";
  let indexName = "bill questions";
  let dateTimeFormat = new Intl.DateTimeFormat("en-us", {
    year: "2-digit",
    month: "2-digit",
    day: "2-digit",
    hour: "numeric",
    minute: "numeric",
    hour12: true,
    timeZone: "EST",
  });

  function formatted_date(timestamp) {
    let date = Date.parse(timestamp) + 360 * 60 * 1000;
    //jesus i hate javascript
    let [
      { value: month },
      ,
      { value: day },
      ,
      { value: year },
      ,
      { value: hour },
      ,
      { value: minute },
      ,
      { value: hour12 },
    ] = dateTimeFormat.formatToParts(date);
    return `${month}.${day}.${year} ${hour}:${minute} ${hour12}`;
  }

  onMount(() => {
    searchClient = algoliasearch(appID, apiKey);

    index = searchClient.initIndex(indexName);
  });

  // Fires on each keyup in form
  async function search() {
    const result = await index.search({ query }.query, {
      hitsPerPage: 100,
      advancedSyntax: true
    });
    totalHits = result.nbHits;
    hits = result.hits;
  }
  async function loadMore() {
    const result = await index.search(query, {
      offset: 100, 
      length: 1000,
      advancedSyntax: true
    });
    extraHits = result.hits;
  }
</script>

<h1>
  ea<span class="blue">s</span>rch
</h1>
search for things
<div>
  <input type="text" bind:value={query} on:input={search} />
</div>

{#each hits as hit}
  {#if hit.question}
    <section>
      <h3>
        <dco>{formatted_date(hit.timestamp)}</dco>
        <qco>
          {@html hit._highlightResult.question.value}
        </qco>
      </h3>
      <p class="answer">
        {@html hit._highlightResult.answer.value}
      </p>
    </section>
  {/if}
{/each}
{#if hits.length && !extraHits.length && hits.length < totalHits }
  <button on:click={loadMore}>load more</button>
{/if}
{#each extraHits as hit}
  {#if hit.question}
    <section>
      <h3>
        <dco>{formatted_date(hit.timestamp)}</dco>
        <qco>
          {@html hit._highlightResult.question.value}
        </qco>
      </h3>
      <p class="answer">
        {@html hit._highlightResult.answer.value}
      </p>
    </section>
  {/if}
{/each}