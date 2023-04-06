<script lang="ts">
    import SearchBar from "./SearchBar.svelte";
    import Loading from "./Loading.svelte";

    let fetching: boolean = false;

    let query: string = ""
    let model: string = "bert"
    async function searchPapers(){
        fetching = true;
        console.log(query)
        const endpoint: string = `http://localhost:8000/search/${model}?query=${query}`
        
        const res = await fetch(
            endpoint,
            {
                method: "GET"
            }
        )
        
        papers = (await res.json())["results"]
        fetching = false;
    }

    
    interface Paper {
		authors: string;
		published: string;
        id: string;
		title: string;
		category: string;
        pdf_url: string
    }

    export let papers: Paper[] = []
</script>


<div data-sveltekit-preload-data="off" class="flex justify-center items-center">
    <div class="text-center">
        <p class="text-4xl mb-10 mt-10">Research Paper Retrieval System</p>
        <div class="mb-10">
            <SearchBar action={searchPapers} bind:value={query}/>
        </div>
        {#if !fetching}
            {#each papers as paper}
                <a target="_blank" href={paper.pdf_url} class="mb-4 block p-4 bg-white border border-gray-200 rounded-lg 
                shadow hover:bg-gray-100 dark:bg-gray-800 
                dark:border-gray-700 dark:hover:bg-gray-700">
                <h5 class="mb-2 text-2xl font-bold tracking-tight text-gray-900 dark:text-white">{paper.title}</h5>
                <p class="font-normal">{paper.authors}</p>
                <p class="font-normal text-gray-700 dark:text-gray-400">{paper.published}</p>
                <div class="bg-slate-900">
                    <p class="font-normal">{paper.category}</p>   
                </div>
                </a>
            {/each}
        {:else}
        <span class="text-center px-2 absolute -translate-y-1/2
                -translate-x-1/2 top-2/4 left-1/2">
            <Loading/>
        </span>
        {/if}
    </div>
</div>
