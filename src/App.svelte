<script>
	import Search from './Search.svelte';
</script>

<main> 
  <Search/>
</main>

<style>

  main {
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
    color: white;
  } 

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
