<script>	
	import Main from './main/main.svelte';
  	import Admin from './admin/admin.svelte';
  	
  	import Router from 'svelte-spa-router';
  
  const routes = {
    '/': Main,
    '/admin': Admin,    
  };
	
</script>

<main>
	  <!-- routes 객체를 라우팅 -->	  
	  <Router {routes} />
</main>

<style>
	main {
		text-align: center;
		padding: 1em;		
		margin: 0 auto;
	}
</style>

<!-- 
<script>
	import { Router, Route, Link, link } from "svelte-routing";
	import Main from "./main/main.svelte";		
	import Admin from "./routes/admin.svelte";
	
	export let url = "";  	

</script>

<main>
	<Router >				 
		<div>			
			<Route path="/"><Main /></Route> 
			<Route path="/admin"><Admin/></Route>
		</div> 		  
		
	</Router>	
</main>

<style>
	main {
		text-align: center;
		padding: 1em;		
		margin: 0 auto;
	}
</style> -->
