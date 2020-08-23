<script>
import { onMount } from 'svelte';
import algoliasearch from 'algoliasearch/lite';

let searchClient;
let index;

let query = '';
let hits = [];
let appID = 'XMYY7X6YSY';
let apiKey = 'e78bc0e48fb18f5972e9e8710d269911';
let indexName = 'bill questions' 
let dateTimeFormat = new Intl.DateTimeFormat('en-us', {
  year: '2-digit',
  month: '2-digit',
  day: '2-digit',
  hour: 'numeric',
  minute: 'numeric',
  hour12: true,
  timeZone: 'EST'
});

function formatted_date(timestamp) {
    let date = Date.parse(timestamp) + 360 * 60 * 1000;
    //jesus i hate javascript
    let [{ value: month },,{ value: day },,{ value: year },,{ value: hour},,{ value: minute},,{ value: hour12}]
     = dateTimeFormat.formatToParts(date);
    return `${month}.${day}.${year} ${hour}:${minute} ${hour12}`
} 

onMount(() => {

	searchClient = algoliasearch(
        appID,
        apiKey
	);

	index = searchClient.initIndex(indexName);

	// Warm up search
	index.search({ query }.query).then(console.log)

});

// Fires on each keyup in form
async function search() {
	const result = await index.search({ query }.query);
	hits = result.hits;
	console.log(hits)
}


</script>

<style>
	em {
		color: darkcyan;
        font-weight: bold;
        font-style: normal;
	}
    .blue {
        color: #00EE3B;
    }
    dco {
        color: #E9EC54;
        padding-right: 0.8em;
    }
    qco {
        color: #B387FF;
    }
    .answer {
        padding-left: 2em;
    }
</style>

<h1>ea<span class="blue">s</span>rch</h1>
search for things
<div>
	<input type="text" bind:value={query} on:keyup={search}>
</div>


{#each hits as hit}
	<section> 
        <h3>
            <dco>{formatted_date(hit.timestamp)}</dco>
            <qco>{@html hit._highlightResult.question.value}</qco>
        </h3>
        <p class="answer">{@html hit._highlightResult.answer.value}</p>
	</section>
{/each}