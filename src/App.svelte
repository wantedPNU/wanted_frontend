<script>
	import { Router, Route } from "svelte-routing";
	import Main from "./main/main.svelte";		  	
</script>

<main>
	<Router>		
		  <Route path="/"><Main /></Route>		
	</Router>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;		
		margin: 0 auto;
	}
</style>