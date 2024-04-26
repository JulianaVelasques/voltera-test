<script lang="ts">
	import { page } from '$app/stores';
	import { goto } from '$app/navigation';
	import { writable } from 'svelte/store';
	import type { Writable } from 'svelte/store';
  
	// Import Markdown files based on the page route
	import { markdown, america, tipografia } from '$lib';
  
	// Read the search query from the URL parameters
	let searchQuery: string = $page.url.searchParams.get('q') || '';
  
	// Store to hold the count of occurrences
	let searchResults: Writable<number> = writable(0);
  
	// Function to search for keywords in the current Markdown file
	const searchMarkdownFile = (): void => {
	  let content: string = '';
	  // Get the content of the Markdown file based on the current page route
	  switch ($page.url.pathname) {
		case '/markdown':
		  content = markdown;
		  break;
		case '/america':
		  content = america;
		  break;
		case '/tipografia':
		  content = tipografia;
		  break;
		default:
		  break;
	  }
  
	  if (searchQuery.trim() !== '') {
		// Count occurrences of the search query in the content
		const occurrences: number = (content.match(new RegExp(searchQuery, 'gi')) || []).length;
  
		// Update searchResults store with the count of occurrences
		searchResults.set(occurrences);
	  } else {
		searchResults.set(0);
	  }
	};
  
	const handleInputChange = (event: Event): void => {
	  event.preventDefault();
	  searchQuery = (event.target as HTMLInputElement).value;
  
	  // Construct the URL with the search query and current page route
	  const params = new URLSearchParams({ q: searchQuery });
	  const url = `${$page.url.pathname}?${params.toString()}`;
	  // Update the URL
	  goto(url, {  noScroll: true, replaceState: true, keepFocus: true });
	  // Perform search
	  searchMarkdownFile();
	};
  
	const clearSearchQuery = (): void => {
	  searchQuery = '';
	  searchResults.set(0);
	};
  
	// Perform initial search when the component mounts
	searchMarkdownFile();
  </script>
  

<!-- Ver o que esse page faz no tutorial -->
<header>
	<nav class="nav-links">
			<li class="forward"><a href="/" on:click={clearSearchQuery} aria-current={$page.url.pathname === '/'}>Home</a></li>
			<li class="forward"><a href="/markdown" on:click={clearSearchQuery} aria-current={$page.url.pathname === '/markdown'}>Markdown</a></li>
			<li class="forward"><a href="/america" on:click={clearSearchQuery} aria-current={$page.url.pathname === '/america'}>América</a></li>
			<li class="forward"><a href="/tipografia" on:click={clearSearchQuery} aria-current={$page.url.pathname === '/tipografia'}>Tipografia</a></li>
	</nav>
</header>

<main>
	<div class="main-box">
		<!-- Search input -->
		{#if ($page.url.pathname !== '/')}
			<div class="search-container">
				<input type="text" bind:value={searchQuery} on:input={handleInputChange} placeholder="Busque por palavras...">
				{#if (searchQuery.trim() !== '')}
					<p class="occurrences">Número de ocorrências: {$searchResults}</p>
				{/if}
			</div>
		{/if}


		<div class="content-box">
			<slot />
		</div>
	</div>

</main>

<footer>
	<div class="footer-links">
		<a href="https://www.linkedin.com/in/julianavelasquesbalta/">
			<i class="fab fa-linkedin"></i>
		</a>
	
		<a href="https://github.com/JulianaVelasques">
			<i class="fab fa-github"></i>
		</a>
	</div>

	<div class="separator"></div>
	<p class="footer-texto">Juliana Velasques - Teste 2024</p>
  </footer>

<style>
	header {
		position: fixed;
		min-width: 100vw;
		top: 0%;
	}

	main {
		margin-top: 6rem;
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.main-box {
		width: 60%;
	}

	.search-container {
		display: flex;
		gap: 1rem;
		margin-bottom: 1rem;
	}

	input[type="text"] {
		padding: 10px 40px 10px 10px;
		border: 1px solid #ccc;
		border-radius: 5px;
		font-size: 0.8rem;
	}

	input[type="text"]:focus {
		border-color: #296C04;
		outline: none;
	}

	.occurrences {
		font-size: 0.7rem;
		color: #296C04;
	}

	.content-box {
		border: solid 1px #ccc;
		padding: 20px;
		overflow: auto;
		height: 30rem;
		border-radius: 1rem;
	}

	.nav-links{
		display: flex;
		align-items: center;
		background: #fff;
		padding: 20px 15px;
		box-shadow: 0 5px 10px rgba(0,0,0,0.1);
	}

	.nav-links li{
		list-style: none;
		margin: 0 12px;
	}

	.nav-links li a{
		position: relative;
		color: #333;
		font-size: 1.1rem;
		padding: 6px 0;
		text-decoration: none;
	}

	.nav-links li a:before{
		content: '';
		position: absolute;
		bottom: 0;
		left: 0;
		height: 3px;
		width: 0%;
		background: #BBF433;
		border-radius: 12px;
		transition: all 0.4s ease;
	}

	.nav-links li a:hover:before{
		width: 100%;
	}

	.nav-links li.forward a:before{
		width: 100%;
		transform: scaleX(0);
		transform-origin: right;
		transition: transform 0.4s ease;
	}

	.nav-links li.forward a:hover:before{
		transform: scaleX(1);
		transform-origin: left;
	}

	footer{
		display: flex;
		flex-direction: column;
		align-items: center;
		position: fixed;
		bottom: 0%;
		min-width: 100vw;
		padding-bottom: 1rem;
	}

	footer a i {
		color: black;
		font-size: 1.3rem;
	}

	.footer-links{
		display: flex;
		gap: 1rem;
		margin-bottom: 0.5rem;
	}

	.separator {
		border: solid 1px #ccc;
		width: 8rem;
		margin-bottom: 0.5rem;
	}

	.footer-texto {
		margin: 0;
	}
</style>
